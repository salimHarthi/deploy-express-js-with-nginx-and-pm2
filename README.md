# These are the steps to deploy express js app with nginx
## If you don't have nodejs
To get this version, you can use the apt package manager. Refresh your local package index by typing:
```
sudo apt update
```
install nodejs and npm if you dont have them in the server
```
sudo apt install nodejs
sudo apt install npm
```

To check which version of Node.js 
```
nodejs -v
```
To check which version of npm
```
npm -v
```

## step #1

install nginx
```
sudo apt-get install nginx
```

## step #2 
Clone your repository on your machine
you can eiter clone or copy you files
```
sudo mkdir /var/www/[your project name]
cd /var/www/[your project name]
```
## step #3
run the following commands to build your react app
```
npm i
npm i pm2 -g
```
PM2 will make our server run in background and if it crashes, PM2 will restart it automatically.
```
pm2 start server.js --name [you can name your app]
```
to check the status of the app 
```
pm2 status
```
## step #4 you can skip this part if you are root
In your project directery
Since you wouldn’t want to use sudo every time you interact with files in /var/www/[your project name], let’s give your user privileges to these folders. 
Constantly using sudo increases the chances of accidentally trashing your system.
```
sudo gpasswd -a "$USER" www-data
sudo chown -R "$USER":www-data /var/www/[your project name]
find /var/www/[your project name] -type f -exec chmod 0660 {} \;
sudo find /var/www/[your project name] -type d -exec chmod 2770 {} \;
```
## step #5
Create a new site in sites available
this command will create a file that you can edit
```
cd /etc/nginx/sites-available
nano [name of your website].com
```
Configure new site
Open krim.com in an editor of your choice and paste the following. 
You can safely use an ip address here.
You can also use both ip and domain.
you can copy the folowing code and change things in the bracket
##### For test deployment Without https
```
server {
  listen [port]; ## put your port number example 80
  server_name [website url]; ## put your website url example oman.com remove this line if you dont have url
  server_name [server IP]; ## put your server IP example 111.111.111.111
  root /var/www/[your project name];
  client_max_body_size 100M;
  location / {
    proxy_pass http://localhost:3000/; ## change the port according to the port you are using locally im using 3000
  }
  ## if you have files that you want to upload/download
  location /uploads/ {
  }
}
```
## step #6
Symlink config
Nginx needs to be told that a given site is active. To do this, create a symlink in sites-enabled
```
cd /etc/nginx/sites-enabled
ln -s ../sites-available/[name of your website].com .
```
## step #7
check if your configuration is ok
```
sudo nginx -t
```
Now start up Nginx!
```
sudo service nginx start
```
If you changed up your repository or made any changes to the configuration, you can restart Nginx with:
```
sudo service nginx restart
```
And you’re done!
