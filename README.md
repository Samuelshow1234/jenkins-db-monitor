# Jenkins DB Monitoring Pipeline

This project provides a **Jenkins pipeline** to monitor disk space and Oracle database tablespaces, as well as check Oracle user password expiry. The pipeline uses **SSH** to connect to a remote server and **SQLPlus** to query the database.

---

## **Features**

- Check disk space for `/u01` and `/u01/dmp` directories on a remote server.
- Check Oracle tablespace usage (Used, Total, Free in MB).
- Check Oracle user password expiry for important users (`APEX_PUBLIC_USER`, `ANONYMOUS`, `ORDS_PUBLIC_USER`).
- Fully configurable using **Jenkins credentials** for secure authentication.

---

## **Prerequisites**

1. **Jenkins** installed (Windows or Linux)  
2. SSH access to the remote server where Oracle DB resides.  
3. Oracle client (`sqlplus`) installed on the remote server.  
4. Jenkins plugins:  
   - **SSH Agent Plugin**  

---

## **Project Structure**




---

## **Step 1: Add Jenkins Credentials**

### 1️⃣ SSH Credentials

- Go to **Jenkins → Credentials → System → Global credentials → Add Credentials**  
- **Kind:** SSH Username with private key  
- **Username:** The SSH user for your DB server (e.g., `opc`)  
- **Private Key:** Either paste directly or upload from file (paste the content) 
- **ID:** Give it a memorable ID, e.g., `credentials-id`  

> ⚠️ This ID will be referenced in the Jenkinsfile in the `sshagent(credentials: ['credentials-id'])` section.




## **Step 2: Configure Jenkins Pipeline**

1. Go to **Jenkins → New Item → Pipeline → OK**  
2. In the **Pipeline** section, go to 
   - **Definition:** Pipeline script from Jenkinsfile (paste it there) 
3. Click **Save**

---

## **Step 3: Update Jenkinsfile**

1. Replace all placeholders with your values:  

| Placeholder          | Description |
|---------------------|-------------|
| `credentials-id`     | The ID of your SSH credential in Jenkins |
| `user`               | SSH username for the remote server |
| `ip`                 | Remote server IP or hostname |
| `sys/Password`       | Oracle username/password (or reference Jenkins credential) |
| `/Path/To/dbhome_1`  | Path to Oracle home on remote server |
| `port/serviceName`   | Oracle listener port and service name |


## **Step 4: Run the Pipeline in Jenkins**

Once your Jenkins pipeline is configured with the repository:

Go to your pipeline job in Jenkins.

Click Build Now in the left sidebar.

Jenkins will execute the following stages automatically:

Check /u01 and /u01/dmp space on the remote server

Check Oracle tablespace usage (Used, Total, Free in MB)

Check Oracle user password expiry for key accounts (APEX_PUBLIC_USER, ANONYMOUS, ORDS_PUBLIC_USER)

You can monitor the output of each stage in Real-time Console Output.

If needed, you can schedule this pipeline to run automatically using Build Triggers → Build periodically in Jenkins.

⚠️ Make sure your Jenkins credentials (SSH and Oracle) are properly configured and the IDs in the Jenkinsfile match those credentials.
