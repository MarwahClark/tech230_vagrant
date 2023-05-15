### Running 2 VM'S

step 1- in the provision files write all the neccessary code needed. 
`#!/bin/bash`
`sudo apt-get update -y`
`sudo apt-get upgrade -y`
`sudo apt-get install nginx -y`
`sudo systemctl start nginx`
`sudo apt-get install python-software-properties -y`
`curl -sL https://deb.nodesource.com/setup_12.x | ``sudo -E bash - `
`sudo apt-get install nodejs -y`
`sudo npm install pm2 -g`

*step 2*- in the vagrant file we have to configure twice as we want 2 virtual machines to run.
# configire 2 so the 2 virtual machines are created, we need a new do block 
#(the config | DO | block)

Vagrant.configure("2") do |config|

  config.vm.define "app" do |app|
  # configures the VM settings
    app.vm.box = "ubuntu/bionic64"
    app.vm.network "private_network", ip:"192.168.10.100"

  # put the app folder from our local machine
    app.vm.synced_folder"app","/home/vagrant/app"

  # provision the VM to have Nginx
    app.vm.provision "shell", path: "provision .sh"
  end
  
  config.vm.define "db" do |db|
    
    db.vm.box = "ubuntu/bionic64"
    db.vm.network "private_network", ip:"192.168.10.150"  #you can pick any number between 1-250

    db.vm.synced_folder "environment", "/home/vagrant/environment"

*step 3* run `vagrant up`

*step 4* once you have ran `Vagrant up` and `vagrant ssh` to enter the vm open gitbash twice and run `vagrant up app/db` on each so you enter the vm for the specific folder

*step 5* in the vm for DB run the following codes
 `sudo apt-get update -y`
`sudo apt-get upgrade -y`
`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927`
`echo "deb https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list`
`sudo apt-get update -y`
`sudo apt-get upgrade -y`
`sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 mongodb-org-mongos=3.2.20 mongodb-org-tools=3.2.20`
`sudo systemctl start mongod`
`sudo systemctl enable mongod`
`sudo systemctl status mongod`
- these codes allow you to sync with mongodb
- *step6* once these codes have been written in the database terminal type code
 `sudo nano/etc/mongod.conf` once in change the `0.0.0.0` to the specific IP address
 - *step 7* type the code
  `sudo systemctl restart mongod` and `sudo systemctl enable mongod`
  - *step 8* enterin the vagrant app termial create an environment variable using the codes 
   `export my_var=hello` and `printenv my_var`
   *step 9* enter the code
    `sudo nano .bashrc`
- *step 10* once accessed scroll down toweards the bottom and enter `export DB_HOST=mongodb://192.168.10.150:27017/posts ` in order to save type `source .bashrc`
-*step11* to double check it has saved run the variable `printenv` and you should find it 
- *step12* enter the app directory using `cd app` 
- `npm install` / `sudo npm install` to cj=heck that the nodes hvae been added
- *step13* type the code `node seeds/seed.js` and then `node app.js` to run your app



### reverse proxy
- once you have your nginx and app running, run the code `sudo nano /etc/nginx/sites-available/default` to set up a file for nginx configuration.
- once you hvae entered the server, scroll down to the location block and change the local host to `3000` instead of `8080`
`    location / {`
`        proxy_pass http://localhost:3000;`
  `      proxy_http_version 1.1;`
 `       proxy_set_header Upgrade $http_upgrade;`
  `      proxy_set_header Connection 'upgrade';`
   `     proxy_set_header Host $host;`
    `    proxy_cache_bypass $http_upgrade;`
    `}`
`}`

- this allows the server to respont to the request at the root.once done save and exit using `control x, y and enter`
-to ensure you dont have any syntax errors run the code `sudo nginx -t`
-making sure your `node app.js` is running run the code `sudo systemctl restart nginx` and test out your servers URL. it should take you to the right page.