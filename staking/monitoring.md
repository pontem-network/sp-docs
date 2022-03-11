# Monitor your node

You can get data such as the height of the chain, the number of connected peers to your node, CPU, memory usage of your machine, and more. To monitor this data, Prometheus is used to collect metrics and Grafana allows for displaying them on the dashboard. The monitoring stack is implemented through docker-compose.

![Grafana Dashboard](/assets/grafana_dashboard.png "Grafana Dashboard")

## Preparation

Make sure your repository with Pontem Boostrap is up to date:
```
cd <path-to-your-pontem-bootstrap-dir>
git pull
```
Check the `docker-compose.yml` file. 
```
cat docker-compose.yml
```
Make sure that it contains these lines: 

![docker-compose.yml](/assets/docker_compose.png "docker-compose.yml")

To be sure that the node is running with the right parameters, restart it:
```
docker-compose up -d
```

## Start monitoring

Run the command:
```
docker-compose -f monitoring.docker-compose.yml up -d
```
After that open `<your-server-ip>:3000` in your browser. Login and password - `admin`.

# Alerting

> Disclaimer! At the moment, the alerting solution is in early beta!

If you want to get a little more control over the node and get alerts in case of problems, you can use this solution.

To make the alerting work you will need:

1. Running monitoring
2. A Discord account
3. Your server in discord
4. Webhook in discord

We'll skip the moment of setting up monitoring (it's described above on this page) and creating an account in Discord. Let's move on to the creation of the server. In the bottom left corner, click on the "+" sign and follow the instructions.

![Discord add server](/assets/discord_add_server.png "Discord add server")

After creating a server, you can use the default channel or create a new one. Next, go to the channel settings and create a webhook. Copy and save it. Do not share it with anyone! 

![Discord edit channel](/assets/discord_edit_channel.png "Discord edit channel")

![Discord create webhook](/assets/discord_create_webhook.png "Discord create webhook")

![Discord copy webhook](/assets/discord_copy_webhook.png "Discord copy webhook")

Copy your Diskord account ID. To do this - click on user settings, then on the triplet and copy. Also save this data.

![Discord copy user ID](/assets/discord_copy_user_id.png "Discord copy user ID")

All that's left is to start the alerting itself. Go to the server where your node is running and execute the commands (replace YOUR_USER_ID and YOUR_DISCORD_WEBHOOK with your values!):

```bash
docker pull pontem/pontem-scripts:latest
docker run -i --rm -v /var/run/docker.sock:/var/run/docker.sock pontem/pontem-scripts:latest alert YOUR_USER_ID YOUR_DISCORD_WEBHOOK
```
