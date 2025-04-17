# Wazuh notes

## User management
Wazuh has 2 types of users: __indexer__(also known as internal users), and __API__. Indexer users query logs, manage indices, and interact with Wazuh Dashboard. So, when we want to configure a user for being able to login to wazuh via dashboard, we must configure these users.
By default, 2 indexer users are created: _admin_ and _kibanauser_. In my case, because I have deployed wazuh with docker installation method, their passwords are configurable from the docker-compose file.\
We can create indexer users by going: ```Wazuh Dashboard Settings > Security > Internal Users > Create Internal User```.
***Pro tip:*** I advice to use only the following special characters: ```. * + ? -```, because it can create some issues Filebeat. Passwords should be normally 8 to 64 characters logs, have a number and a special character.
