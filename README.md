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


https://grafana.com/grafana/dashboards/

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

# use grafana-cli to install plugins

```
sudo grafana-cli plugins install redis-datasource

sudo grafana-cli plugins install mongodb-datasource

sudo grafana-cli plugins install grafana-github-datasource
```

## Grafana Dashboard for Cloudflare Analytics and Overview

12167

https://grafana.com/grafana/dashboards/12167  

## Dashboard 9614 NGINX Ingress controller reading prometheus

9614 NGINX Ingress controller reading prometheus

https://github.com/kubernetes/ingress-nginx/tree/master/deploy/grafana/dashboards


## Dashboard 1 Linux Stats with Node Exporter. Monitoring Linux host metrics

14731

https://grafana.com/grafana/dashboards/14731

https://github.com/3xploitGuy/linuxstats

## Dashboard Monitoring Linux host metrics

https://prometheus.io/docs/guides/node-exporter/


---
title: Monitoring Linux host metrics with the Node Exporter
---

# Monitoring Linux host metrics with the Node Exporter

The Prometheus [**Node Exporter**](https://github.com/prometheus/node_exporter) exposes a wide variety of hardware- and kernel-related metrics.

In this guide, you will:

* Start up a Node Exporter on `localhost`
* Start up a Prometheus instance on `localhost` that's configured to scrape metrics from the running Node Exporter

NOTE: While the Prometheus Node Exporter is for *nix systems, there is the [Windows exporter](https://github.com/prometheus-community/windows_exporter) for Windows that serves an analogous purpose.

## Installing and running the Node Exporter

The Prometheus Node Exporter is a single static binary that you can install [via tarball](#tarball-installation). Once you've downloaded it from the Prometheus [downloads page](/download#node_exporter) extract it, and run it:

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v*/node_exporter-*.*-amd64.tar.gz
tar xvfz node_exporter-*.*-amd64.tar.gz
cd node_exporter-*.*-amd64
./node_exporter
```

You should see output like this indicating that the Node Exporter is now running and exposing metrics on port 9100:

```
INFO[0000] Starting node_exporter (version=0.16.0, branch=HEAD, revision=d42bd70f4363dced6b77d8fc311ea57b63387e4f)  source="node_exporter.go:82"
INFO[0000] Build context (go=go1.9.6, user=root@a67a9bc13a69, date=20180515-15:53:28)  source="node_exporter.go:83"
INFO[0000] Enabled collectors:                           source="node_exporter.go:90"
INFO[0000]  - boottime                                   source="node_exporter.go:97"
...
INFO[0000] Listening on :9100                            source="node_exporter.go:111"
```

## Node Exporter metrics

Once the Node Exporter is installed and running, you can verify that metrics are being exported by cURLing the `/metrics` endpoint:

```bash
curl http://localhost:9100/metrics
```

You should see output like this:

```
# HELP go_gc_duration_seconds A summary of the GC invocation durations.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 3.8996e-05
go_gc_duration_seconds{quantile="0.25"} 4.5926e-05
go_gc_duration_seconds{quantile="0.5"} 5.846e-05
# etc.
```

Success! The Node Exporter is now exposing metrics that Prometheus can scrape, including a wide variety of system metrics further down in the output (prefixed with `node_`). To view those metrics (along with help and type information):

```bash
curl http://localhost:9100/metrics | grep "node_"
```

## Configuring your Prometheus instances

Your locally running Prometheus instance needs to be properly configured in order to access Node Exporter metrics. The following [`prometheus.yml`](../../prometheus/latest/configuration/configuration/) example configuration file will tell the Prometheus instance to scrape, and how frequently, from the Node Exporter via `localhost:9100`:

<a id="config"></a>

```yaml
global:
  scrape_interval: 15s

scrape_configs:
- job_name: node
  static_configs:
  - targets: ['localhost:9100']
```

To install Prometheus, [download the latest release](/download) for your platform and untar it:

```bash
wget https://github.com/prometheus/prometheus/releases/download/v*/prometheus-*.*-amd64.tar.gz
tar xvf prometheus-*.*-amd64.tar.gz
cd prometheus-*.*
```

Once Prometheus is installed you can start it up, using the `--config.file` flag to point to the Prometheus configuration that you created [above](#config):

```bash
./prometheus --config.file=./prometheus.yml
```

## Exploring Node Exporter metrics through the Prometheus expression browser

Now that Prometheus is scraping metrics from a running Node Exporter instance, you can explore those metrics using the Prometheus UI (aka the [expression browser](/docs/visualization/browser)). Navigate to `localhost:9090/graph` in your browser and use the main expression bar at the top of the page to enter expressions. The expression bar looks like this:

![Prometheus expressions browser](/assets/prometheus-expression-bar.png)

Metrics specific to the Node Exporter are prefixed with `node_` and include metrics like `node_cpu_seconds_total` and `node_exporter_build_info`.

Click on the links below to see some example metrics:

Metric | Meaning
:------|:-------
[`rate(node_cpu_seconds_total{mode="system"}[1m])`](http://localhost:9090/graph?g0.range_input=1h&g0.expr=rate(node_cpu_seconds_total%7Bmode%3D%22system%22%7D%5B1m%5D)&g0.tab=1) | The average amount of CPU time spent in system mode, per second, over the last minute (in seconds)
[`node_filesystem_avail_bytes`](http://localhost:9090/graph?g0.range_input=1h&g0.expr=node_filesystem_avail_bytes&g0.tab=1) | The filesystem space available to non-root users (in bytes)
[`rate(node_network_receive_bytes_total[1m])`](http://localhost:9090/graph?g0.range_input=1h&g0.expr=rate(node_network_receive_bytes_total%5B1m%5D)&g0.tab=1) | The average network traffic received, per second, over the last minute (in bytes)


# Grafana Digitalocean

by Maheen Aboobakkar | Jun 5, 2022

Grafana in digitalocean is an open-source data visualization and monitoring tool that integrates with complex data.

As part of our Digitalocean managed service, Bobcares responds to all inquiries, no matter how big or small.

Let’s take a closer look at Grafana in digitalocean’s specifics.

### Grafana in digitalocean

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
