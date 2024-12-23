# README: Deploying a Node.js Application on AWS Elastic Beanstalk

## **Overview**
This guide outlines the steps to deploy a simple Node.js web application on AWS Elastic Beanstalk, including setting up HTTPS and monitoring for security and reliability.

---

## **Prerequisites**

1. AWS account with sufficient permissions.
2. AWS CLI and EB CLI installed on your local machine.
3. Node.js installed on your local machine.
4. Domain name for HTTPS setup (optional).

---

## **Step-by-Step Deployment Instructions**

### **1. Set Up Your Environment**

1. **Install AWS CLI**:
   - [Download and install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).
   - Verify installation:
     ```bash
     aws --version
     ```

2. **Configure AWS CLI**:
   ```bash
   aws configure
   ```
   Provide the following:
   - Access Key ID and Secret Access Key
   - Default region: `us-east-1`
   - Output format: `json`

3. **Install Elastic Beanstalk CLI**:
   - Install EB CLI:
     ```bash
     pip install awsebcli
     ```

4. **Verify EB CLI Installation**:
   ```bash
   eb --version
   ```

---

### **2. Create Your Node.js Application**

1. **Set Up the App Directory**:
   ```bash
   mkdir my-node-app
   cd my-node-app
   npm init -y
   npm install express
   ```

2. **Create `app.js`**:
   ```javascript
   const express = require('express');
   const app = express();
   const port = process.env.PORT || 3000;

   app.get('/', (req, res) => {
       res.send('Hello, World from AWS Elastic Beanstalk!');
   });

   app.listen(port, () => {
       console.log(`App running on http://localhost:${port}`);
   });
   ```

3. **Prepare Deployment Files**:
   - Create a `.gitignore` file:
     ```plaintext
     node_modules/
     *.log
     ```
   - Create a `Procfile` to specify the start command:
     ```plaintext
     web: node app.js
     ```

---

### **3. Deploy the Application Using Elastic Beanstalk**

1. **Initialize Elastic Beanstalk**:
   ```bash
   eb init
   ```
   - Choose `Node.js` as the platform.
   - Select `us-east-1` as the default region.
   - Create a new application name (e.g., `my-node-app`).

2. **Create the Environment**:
   ```bash
   eb create my-node-env
   ```
   - Elastic Beanstalk will provision resources (EC2, Load Balancer, etc.) for your app.

3. **Deploy the App**:
   ```bash
   eb deploy
   ```

4. **Access the Application**:
   - Once deployment is complete, Elastic Beanstalk provides a URL:
     ```
     http://my-node-env.us-east-1.elasticbeanstalk.com
     ```
   - Open this URL in a browser to see your app.

---

### **4. Enable HTTPS**

1. **Request an SSL Certificate**:
   - Open the [ACM Console](https://console.aws.amazon.com/acm/home).
   - Request a public certificate:
     - Enter your domain name (e.g., `my-app.example.com`).
     - Choose **DNS Validation**.
   - Add the provided CNAME record to your domain's DNS settings to validate.
   - Wait until the status changes to **Issued**.

2. **Attach the Certificate to the Load Balancer**:
   - Go to the [Elastic Beanstalk Console](https://console.aws.amazon.com/elasticbeanstalk).
   - Select your environment > **Configuration > Load Balancer**.
   - Add a listener for port 443:
     - Protocol: **HTTPS**
     - SSL Certificate: Select the issued certificate from ACM.
   - Redirect HTTP (port 80) to HTTPS.

3. **Update DNS Records**:
   - Point your domain to the Elastic Load Balancer:
     - Create an **A Record** or **CNAME Record** with your DNS provider.

---

### **5. Enable Monitoring**

1. **Set Up CloudWatch Logs**:
   - Enable log streaming:
     - Elastic Beanstalk Console > **Monitoring > Enable Logs**.
   - View logs in the [CloudWatch Logs Console](https://console.aws.amazon.com/cloudwatch/home).

2. **Create CloudWatch Alarms**:
   - Go to [CloudWatch Alarms](https://console.aws.amazon.com/cloudwatch/home).
   - Create alarms for key metrics:
     - **CPUUtilization** > 80%
     - **Latency** > 2 seconds
     - **Error Rates** > 5 errors/min.
   - Configure notifications using an SNS topic.

3. **Enable Enhanced Health Monitoring**:
   - Elastic Beanstalk Console > **Configuration > Monitoring**.
   - Enable Enhanced Health Monitoring.

---

### **6. Secure Environment**

1. **Environment Variables**:
   - Store sensitive data:
     - Elastic Beanstalk Console > **Configuration > Software > Environment Properties**.
     - Add key-value pairs (e.g., `DB_USER`, `DB_PASSWORD`).

2. **Restrict SSH Access**:
   - Go to **EC2 > Security Groups**.
   - Edit the security group to allow SSH access only from your IP.

3. **Set Up Auto Scaling**:
   - Elastic Beanstalk Console > **Configuration > Auto Scaling**.
   - Configure instance scaling based on CPU utilization.

---

### **Deliverables**

1. **Deployment URL**:
   - The public HTTPS URL of your live application.

2. **Documentation**:
   - Include this README for setup and maintenance reference.

---

### **Notes**
- This deployment is configured for cost-efficiency and scalability.
- For production, ensure your database, backups, and sensitive information are properly secured.

---



