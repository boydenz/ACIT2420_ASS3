# assignment 3 for linux

## Creating a New Regular User

### Creating a user:

To create a new user, replace ‘NEWUSER’ with your desired username. 

```bash
useradd -ms /bin/bash NEWUSER 
```

The user’s login shell will be set to /bin/bash. 

Alternatively, if you do forget to add /bin/bash in useradd, you can adjust the user’s login shell with:

```bash
chsh -s /bin/bash NEWUSER
```

### Giving the new user elevated privileges

To allow your new user to perform administrative tasks, we will want to modify our user and add it to the group “sudo”.

```bash
usermod -aG sudo NEWUSER
```

### Accessing the server via SSH

To allow your new user to connect to the server via SSH, we need to copy the SSH directory to the new user’s home directory. 

```bash
cp -r /root/.ssh /home/NEWUSER
```

Now you will want to change the ownership of the /.ssh so that the user owns it.

```bash
chown -R NEWUSER:NEWUSER /home/.ssh
```

You can then exit your server and use the following to access the server via SSH.

```bash
ssh -i PATH-TO-YOUR-KEY NEWUSER@IP-ADDRESS-OF-DEBIAN
```

## Preventing root user from connecting to the server via SSH

## Locking the Root User

You can edit your SSH configuration file so that the root user can no longer connect to the server via SSH. This is done by editing it in vim or nano. In this example, we will use vim. We will require sudo as we are editing a config file. 

```bash
sudo vim /etc/ssh/sshd_config
```

Once opened, we will look for the line that says **PermitRootLogin** and change it by pressing “i” for insert, and replacing “yes” to “no”. 

We will restart the shell and that should prevent the root user from accessing the server via SSH. 

```bash
systemctl restart ssh.service
```

## Installing nginx

To install nginx, we will run the following command: 

```bash
sudo apt install nginx
```

## Configuring nginx to serve a sample website

To configure nginx to serve a sample website, we will want to make a new directory called “my-site” in /var/www.

```bash
sudo mkdir /var/www/my-site
```

Now we will want to make a new file in the my-site directory called “index.html”

```bash
sudo vim /var/www/my-site/index.html
```

Using vim to create the file will automatically open it, so you can paste the following inside the file:

```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
        }
    </style>
</head>
<body>
    <h1>Hello, World</h1>
</body>
</html>
```

Now create a new file in /etc/nginx/sites-available called my-site.conf 

```bash
sudo vim /etc/nginx/sites-available/my-site.conf
```

Paste the following into my-site.conf

```bash
server {
	listen 80;
	
	root /var/www/my-site;
	
	index index.html;
	
	server_name _;
	
	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
}
```

Create a symbolic link to your new config file from:

/etc/nginx/sites-available **to** /etc/nginx/sites-enabled

```bash
sudo ln -s /etc/nginx/sites-available/my_site.conf /etc/nginx/sites-enabled
```

Test the configuration and restart nginx.

```bash
sudo nginx -t
sudo systemctl restart nginx
```

Now you can run your website from the IP address you used to connect to the server. Alternatively, you can also use the ‘curl’ command to see the html raw.

```bash
curl IP-ADDRESS
```