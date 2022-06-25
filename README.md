# grafana-monitoring

# 🚀 Javascript full-stack 🚀

https://github.com/coding-to-music/grafana-monitoring

https://grafana-monitoring.vercel.app

From / By previous articles:

[Setting up an example Grafana dashboard which queries Prometheus for data. Set up a Prometheus data source and create a Prometheus Graph in a new Grafana Dashboard #211](https://github.com/coding-to-music/coding-to-music.github.io/issues/211)

[Cloud Home Base Setup - Data ingestion, processing, storage, display #213](https://github.com/coding-to-music/coding-to-music.github.io/issues/213)

[Installation, Starting and Stopping Grafana via systemd and init.d for Debian and Ubuntu #224](https://github.com/coding-to-music/coding-to-music.github.io/issues/224)

[Monitoring Stack: Grafana, Alertmanager, Prometheus, Node Exporter, ZnapZend Exporter, Ansible #279](https://github.com/coding-to-music/coding-to-music.github.io/issues/279)

[Telegraf, InfluxDB, and Grafana topology monitoring Cloudflare #300](https://github.com/coding-to-music/coding-to-music.github.io/issues/300)

[![Grafana screenshot](https://prometheus.io/assets/grafana_prometheus.png)](https://prometheus.io/assets/grafana_prometheus.png)

https://grafana.com/grafana/dashboards/12167

## Environment variables:

```java
##
# Configurations
##
# Endpoint URL for InfluxDB
InfluxDBURL="YOURINFLUXSERVERIP" #Your InfluxDB Server, http://FQDN or https://FQDN if using SSL
InfluxDBPort="8086" #Default Port
InfluxDB="telegraf" #Default Database
InfluxDBUser="USER" #User for Database
InfluxDBPassword="PASSWORD" #Password for Database

# Endpoint URL for login action
cloudflareapikey="YOURAPIKEY"
cloudflarezone="YOURZONEID"
cloudflareemail="YOUREMAIL"
```

## GitHub

```java
git init
git add .
git remote remove origin
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:coding-to-music/grafana-monitoring.git
git push -u origin main
```

#

```

```

Grafana Digitalocean | All about
by Maheen Aboobakkar | Jun 5, 2022

Grafana in digitalocean is an open-source data visualization and monitoring tool that integrates with complex data.

As part of our Digitalocean managed service, Bobcares responds to all inquiries, no matter how big or small.

Let’s take a closer look at Grafana in digitalocean’s specifics.

# Grafana in digitalocean

by Maheen Aboobakkar | Jun 5, 2022

https://bobcares.com/blog/grafana-digitalocean/

Grafana is a free data visualisation and monitoring tool that works with complicated data from Prometheus, InfluxDB, Graphite, and ElasticSearch. This allows us to build alerts, notifications, and ad-hoc filters for our data, as well as facilitating collaboration with our colleagues via built-in sharing options.

## Installing Grafana

1. Firstly, use wget to download the Grafana GPG key, then pipe the output to apt-key.

```
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```

1. Then, in the APT sources, add the Grafana repository:

```
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
```

1. To keep our package listings up to date, we need to refresh the APT cache:

```
sudo apt update
```

1. Then, we can begin the installation:

```
sudo apt install grafana
```

1. Once Grafana is installed, start the Grafana server with systemctl:

```
sudo systemctl start grafana-server
```

1. Then, check the status of Grafana to see if it’s up and running:

```
sudo systemctl status grafana-server
```

1. Finally, set the service to launch Grafana automatically when the server boots up:

```
sudo systemctl enable grafana-server
```

Grafana is up and running and ready to use right now. After that, we’ll protect our connection to Grafana with a reverse proxy and an SSL certificate.

## Configuring the Reverse Proxy

By encrypting the connection to and from Grafana, an SSL certificate will ensure that our data is secure. However, in order to leverage this connection, we must first configure Nginx as a reverse proxy for Grafana.

1. Firstly, go to the Nginx configuration file and open it.

```
sudo nano /etc/nginx/sites-available/domain_name
```

1. Then, in this location block, delete the existing try_files line and replace it with the proxy_pass option:

```
proxy_pass http://localhost:3000;
```

1. Then, use CTRL+X, Y, and ENTER to save and close the file.
   Now test the modified settings to ensure that everything is working properly:

```
sudo nginx -t
```

1. Finally, reload Nginx to activate the changes:

```
sudo systemctl reload nginx
```

By typing https://domain_name into our web browser, we can now view the default Grafana login screen. If we can’t connect to Grafana, double-check that our firewall is set to allow traffic on port 443, then retrace the steps above.

## Credentials Updating

Because every Grafana installation utilises the same administrative credentials by default, changing our login information as soon as feasible is a smart practise.

1. Firstly, open a web browser and go to https://domain_name.
2. On the default login screen, the Grafana logo, a form asking for an Email or username and password, a Log in button, and a Forgot our password link will appear.
3. Then, in the Email or username and Password fields, type admin.
4. Then click on the Log in button.
5. On the next screen, we’ll be asked to change the default password to make our account more secure:
6. In the New password and Confirm new password fields, type the password we want to use.
7. We can either click Submit to save the new information or skip this step by pressing Skip.
8. Click Submit to increase the security of our Grafana setup. We’ll go to the Grafana dashboard’s Welcome page.
9. Disable Grafana Registrations and Anonymous access.
10. Visitors can create user accounts for themselves and preview dashboards without registering with Grafana. We may want to enable these features when Grafana is not accessible via the internet or when it is working with publicly available data, such as service statuses. However, when working with sensitive data in Grafana online, anonymous access may pose a security risk. Make some changes to our Grafana configuration to resolve this issue.

11. Firstly, edit Grafana’s main configuration file:

```
sudo nano /etc/grafana/grafana.ini
```

12. Then, Under the [users] heading, look for the allow_sign_up directive:

```
[users]
# disable user signup / registration
allow_sign_up = false
```

1. When this directive is set to true, it adds a Sign Up button to the login screen, allowing users to register and access Grafana. By setting this directive to false, we can remove the
   Remove the ; at the beginning of the line to uncomment this directive, and then set the option to false.
   Then, under the [auth.anonymous] heading, look for the enabled directive:

```
[auth.anonymous]
# enable anonymous access
;enabled = false
```

1. When enabled is set to true, non-registered users can access our dashboards. When set to false, only registered users can access our dashboards.
   Remove the ; at the beginning of the line and set the option to false to uncomment this directive.

1. Finally, Exit the text editor after saving the file.
   Restart Grafana to see the changes take effect:

```
sudo systemctl restart grafana-server
```

1. Check Grafana’s service status to make sure everything is working:

```
sudo systemctl status grafana-server
```

As before, the output will indicate that Grafana is active (running).
