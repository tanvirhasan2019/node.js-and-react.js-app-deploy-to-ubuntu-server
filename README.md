# Node.js and React.js App Deployment Guide

🚀 **Quick Start Guide**

To deploy your Node.js and React.js application on an Ubuntu server, follow these steps:

1. **Install NVM (Node Version Manager):**

   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
   ```

2. **Install the Latest Node.js Version:**

   ```bash
   nvm install node
   ```

3. **Install MongoDB:**

   ```bash
   sudo apt update
   sudo apt install gnupg wget apt-transport-https ca-certificates software-properties-common
   wget -qO- https://www.mongodb.org/static/pgp/server-7.0.asc | sudo apt-key add -
   echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
   sudo apt update
   sudo apt install mongodb-org
   sudo systemctl enable --now mongod
   ```

4. **Configure MongoDB:**

   - Edit the MongoDB configuration file `/etc/mongod.conf` and enable authorization.

   ```bash
   sudo nano /etc/mongod.conf
   # Uncomment the following line:
   security:
     authorization: enabled
   ```

   - Restart MongoDB service:

   ```bash
   sudo systemctl restart mongod
   ```

5. **Create Administrative MongoDB User:**

   - Access the mongo shell:

   ```bash
   mongosh
   ```

   - Inside the shell, create an administrative user:

   ```javascript
   use admin
   db.createUser({
     user: "username",
     pwd: passwordPrompt(),
     roles: [
       { role: "userAdminAnyDatabase", db: "admin" },
       { role: "readWriteAnyDatabase", db: "admin" }
     ]
   })
   ```

   - Exit the shell and login with the created user:

   ```bash
   mongosh --host localhost:27017 -u username -p
   ```

6. **Install Nginx:**

   ```bash
   sudo apt-get update
   sudo apt-get install nginx
   ```

7. **Install PM2 (Production Process Manager) and Certbot:**

   ```bash
   npm install pm2@latest -g
   sudo snap install --classic certbot
   sudo ln -s /snap/bin/certbot /usr/bin/certbot
   ```

8. **Deploy and Configure Your App:**

   - Clone your frontend and backend repositories.
   - Install dependencies and build frontend:

   ```bash
   cd frontend && npm install && npm run build
   ```

   -create process.yml script file to run your backend server

   ```bash
   apps:
   ```

- script: '/backend project path/index.js'
  exec_mode: 'fork'
  name: 'worker-0'
  env:
  PORT: 3500
  NODE_ENV: production
  db: "database url"
  jwtPrivateKey: "jwt-test"
- script: '/backend project path/index.js'
  exec_mode: 'fork'
  name: 'worker-1'
  env:
  PORT: 3501
  NODE_ENV: production
  db: "database url"
  jwtPrivateKey: "jwt-test"
- script: '/backend project path/index.js'
  exec_mode: 'fork'
  name: 'worker-2'
  env:
  PORT: 3502
  NODE_ENV: production
  db: "database url"
  jwtPrivateKey: "jwt-test

  ````

  - Start your script file with PM2:

  ```bash
  pm2 start process.yml
  ````

9. **Configure Nginx:**

   - Edit the Nginx configuration file `/etc/nginx/sites-available/default`.
   - Configure Nginx as a reverse proxy with load balancing.

   -Sample code of nginx configuration file

   ````bash
   upstream loadbalancer {
       least_conn; #increase port list based on your cpu cores
       server localhost:port1;
       server localhost:port2;
       server localhost:port3;
    }

    server {

      index index.html index.htm index.nginx-debian.html;

      server_name example.com www.example.com;

    # react app & front-end files

    location / {
            root /var/www/html/build;
            try_files $uri /index.html;
    }
    location /api/ {
            proxy_pass http://loadbalancer/;
            proxy_buffering         on;

    }
   }```

    - Test Nginx configuration and reload Nginx:
    ```bash
    sudo nginx -t
    sudo systemctl reload nginx
    ```

   ````

10. **Set Up SSL Certificate with Certbot:**

    ```bash
    sudo certbot --nginx
    ```

11. **Check Your Domain:**
    Visit your domain in a web browser to verify the setup.

📝 **README Structure**

1. Introduction
2. Prerequisites
3. Installation and Updating
   - Installing NVM
   - Installing MongoDB
   - Configuring MongoDB
   - Creating Administrative MongoDB User
   - Installing Nginx
   - Installing PM2 and Certbot
4. Deploying Your App
5. Configuring Nginx
6. Setting Up SSL with Certbot
7. Testing
8. Conclusion

🛠 **Contributing**

Feel free to contribute to improve this guide!
