# IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS).

## CLIENT-SERVER ARCHITECTURE WITH MYSQL

Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another.
In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server".
A simple diagram of Web Client-Server architecture is presented below:

![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/Screenshot 2023-10-30 233056.png>)
Assuming that you go on your browser, and typed in there www.propitixhomes.com. It means that your browser is considered the "Client". Essentially, it is sending requests to the remote server, and in turn, would be expecting some kind of response from the remote server.
Let’s take a very quick example and see Client-Server communicatation in action.
Open up your Ubuntu or Windows terminal and run the curl command:
 curl -Iv www.propitixhomes.com
Note: If your Ubuntu does not have ‘curl’, you can install it by running sudo apt install curl
In this example, your terminal will be the client, while www.propitixhomes.com will be the server.
See the response from the remote server in the below output. You can also see that the requests from the URL are being served by a computer with an IP address 160.153.133.153 on port 80.

![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/crul-propi.png>)

## To demonstrate a basic client-server using MySQL Relational Database Management System (RDBMS), follow the below instructions

### Introduction
In this project, you will implement a basic client-server architecture using the MySQL Database Management System (DBMS). You will create two Linux-based virtual servers in AWS: one for the MySQL server and one for the MySQL client. The MySQL server will host the database, while the MySQL client will connect to it. This project will help you understand the fundamentals of setting up a client-server architecture using MySQL.

### Prerequisites
Before you start, make sure you have the following:

An AWS account with EC2 instances created.
Basic knowledge of Linux terminal commands.
MySQL Server and MySQL Client software installed on the respective virtual servers.

### Step 1: Create Virtual Servers
Create two Linux-based virtual servers on AWS:

Server A (MySQL Server): Name it 'mysql server'.

Server B (MySQL Client): Name it 'mysql client'.

![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/Instance.png>)


## Step 2: Install MySQL Server and MySQL Client

In this step, you will install MySQL Server software on the 'mysql server' and MySQL Client software on the 'mysql client' to set up the MySQL client-server architecture.

### Installing MySQL Server on 'mysql server'

1. **SSH into 'mysql server':** Open your terminal and use SSH to connect to 'mysql server.' Replace `[your_key.pem]` and `[your_server_ip]` with your own key and server IP address:

    ```bash
    ssh -i [your_key.pem] ubuntu@[your_server_ip]
    ```

2. **Update Package Lists:** It's a good practice to ensure your package lists are up-to-date. Run the following command:

    ```bash
    sudo apt update
    ```

3. **Install MySQL Server:** Use the following command to install the MySQL Server:

    ```bash
    sudo apt install mysql-server
    ```

   During the installation, you will be prompted to set a root password for the MySQL Server.

4. **Secure MySQL Installation:** It is recommended to secure the MySQL installation by running the MySQL security script:

    ```bash
    sudo mysql_secure_installation
    ```

   Follow the prompts to configure the security options. This script will help you set a root password, remove anonymous users, disallow root login remotely, and remove the test database.

![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/install msql.png>)


### Installing MySQL Client on 'mysql client'

1. **SSH into 'mysql client':** Open your terminal and use SSH to connect to 'mysql client.' Replace `[your_key.pem]` and `[your_server_ip]` with your own key and server IP address:

    ```bash
    ssh -i [your_key.pem] ubuntu@[your_server_ip]
    ```

2. **Update Package Lists:** Ensure your package lists are up-to-date by running:

    ```bash
    sudo apt update
    ```

3. **Install MySQL Client:** Use the following command to install the MySQL Client:

    ```bash
    sudo apt install mysql-client
    ```

   This will install the MySQL client software on 'mysql client' so that it can connect to the MySQL Server on 'mysql server.'

![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/install client.png>)


## Step 3: Configure Network Communication
Since both servers are on the same local virtual network, they can communicate using local IP addresses. Use 'mysql server's' local IP address to connect from 'mysql client.' MySQL Server typically uses TCP port 3306, so you need to open it by configuring 'Inbound rules' in the 'mysql server' Security Groups. For added security, restrict access to only the local IP address of 'mysql client.


![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/tcp3306.png>)

## step 4: Create a Database and User

1. **Connect to the MySQL Server**: Ensure you are connected to the MySQL server from the MySQL client using the following command. Replace `<username>`, `<MySQL server IP>`, and `<password>` with your actual credentials.

    ```bash
    mysql -u <username> -h <MySQL server IP> -p
    ```

2. **Enter Password**: You will be prompted to enter the password for the MySQL user. Once entered, you should be connected to the MySQL server.

3. **Create a Database**: To create a new database, use the following SQL command. Replace `<mydatabase>` with the desired name of your database.

    ```sql
    CREATE DATABASE mydatabase;
    ```

4. **Create a User**: Create a user who will have access to the database. Replace `<myuser>` with the desired username and `<mypassword>` with the user's password.

    ```sql
    CREATE USER 'myuser'@'%' IDENTIFIED BY 'mypassword';
    ```

5. **Grant Privileges**: Grant the user all privileges for the previously created database. This allows the user to manage the database.

    ```sql
    GRANT ALL PRIVILEGES ON mydatabase.* TO 'myuser'@'%';
    ```


6. **Exit MySQL**: To exit the MySQL prompt, use the `exit` command.

    ```sql
    exit
    ```
![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/mysql -u example_user -p.png>)

## Step 4: Configure MySQL Server for Remote Access

In this step, you will configure 'mysql server' to allow connections from remote hosts by editing the MySQL configuration file.

1. **SSH into 'mysql server':** Open your terminal and use SSH to connect to 'mysql server.' Replace `[your_key.pem]` and `[your_server_ip]` with your own key and server IP address:

    ```bash
    ssh -i [your_key.pem] ubuntu@[your_server_ip]
    ```

2. **Edit the MySQL Configuration File:** Use a text editor to open the MySQL configuration file for editing. You can use `vi` or any text editor you prefer. For example, using `vi`:

    ```bash
    sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
    ```

3. **Modify the `bind-address`:** In the configuration file, locate the line that reads 'bind-address = 127.0.0.1.' This line specifies that MySQL should only listen for connections on the localhost (127.0.0.1). To allow remote connections, replace '127.0.0.1' with '0.0.0.0':

    ```bash
    bind-address = 0.0.0.0
    ```

   This change tells MySQL to listen on all available network interfaces, including external ones.

4. **Save and Exit:** If you're using `vi`, press `Esc`, then type `:wq` to save the changes and exit the text editor.

5. **Restart MySQL:** To apply the changes, you need to restart the MySQL service:

    ```bash
    sudo service mysql restart
    ```
![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/vim.png>)

Now, MySQL Server is configured to allow remote connections. This change allows 'mysql client' to connect to 'mysql server' from a remote location. Make sure you have secured the server and its credentials for these remote connections to maintain the security of your database.



### Step 5: Connect Remotely from MySQL Client
From 'mysql client,' connect remotely to 'mysql server's' MySQL Database Engine without using SSH. Use the mysql utility to perform this action. For example:

bash
Copy code
mysql -h [mysql server's local IP address] -u [username] -p
Enter the password when prompted. Verify the Connection
Check that you have successfully connected to the remote MySQL server and can perform SQL queries. For example, run:

sql
Copy code
SHOW DATABASES;
If you see a list of databases, then you have successfully completed the project and deployed a fully functional MySQL Client-Server setup.

![Alt text](<CLIENT-SERVER ARCHITECTURE WITH MYSQL/IMAGES/show database.png>)