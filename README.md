# Wazuh notes

> [!NOTE]
> The following Wazuh notes were taken for mostly focusing on PCI-DSS Compliance, but information mentioned here is applicable for other uses. Also, this is not intended to replace Wazuh documentation in any way, rather, the reader should understand, that this is an extension of another fellow internet user, who, while facing common issues and finding their solutions, is providing these notes publicly in a hope, that it will help others.

---

> [!WARNING]
> The following information was tested and applied to **single-node** Docker Wazuh deployment stack. Commands & file paths mentioned here may not work on other deployments. Do the research, and if you want, you can add your notes, information or experienced issue and its solution here. Please, inform me in that case.

---

The whole configuration itself, as a first starter, was pretty confusing for me, so I would like to clear out some things. On a basic level, wazuh has three major central components: **Wazuh Manager**, **Wazuh Dashboard** and **Wazuh indexer**. If we choose to deploy it as a single-node stack, it deploys one Wazuh manager, Indexer, and Dashboard node. Wazuh indexer is **based on Opensearch**, which is itself a fork of Open source Elasticseach 7.10, but in here, it is preconfigured in wazuh-specific way, Supports PCI-DSS Requirement 10.5.1 - Log retention, is managed with Wazuh Dashboard. That is why, when navigating Wazuh dashboards, you will see some parts of help are redirected to Opensearch documentation. This question was bugging me a lot, so I did my research. On the other hand, Wazuh Dashboard is **based on OpenSearch Dashboards**, enhanced with a Wazuh-specific plugin to support security monitoring features, And Opensearch Dashboards is a fork of Kibana, which built on Node.JS and React. You will have fun looking at Axios errors in your dashboard, popping up like a balloon in red, in the lower right corner :D


## User management and RBAC

As noted in the Documentation, The operation of RBAC relies on the interaction among four key components: users, roles, rules and policies. 

* **Users** - This is you, me, or whatever. They can be service users as well. Whatever sends a request, and gets a response, is a user.
* **Roles** - Currently, I think about them like I would think of them as groups. I use them to "group" together users, which need to share same permissions. For PCI-DSS, I have 2 custom rules configured in Indexer Management: Security Engineers(essentially, admins) and Security analysts(Users, that only have read access). Read The Roles section below for more information, if this is what confuses you.
* **Rules** - Determine the conditions under which poicies apply. You can select under ```effect``` to either allow or deny.
* **Policies or Permissions** - Individual actions, that allow us to perform a certain thing/action. Under ```Indexer Management```, these are called *Permissions*, and in ```Server Management```, they are policies. The configuration of the latter one is similar to a firewall rule logic, having options to allow or deny the actions that we have created.


### Users

In Wazuh as a whole, if we go to Dashboard, click the sandwich menu button, we can find 2 tabs: ```Server Management > Security``` and ```Indexer Management > Security```. In both, we have roles, which are an essential part of Wazuh RBAC system. They govern user permissions for different components of the Wazuh platform. So, what is the different between those two?

### Roles
**Server management roles** - Control Access to Wazuh Manager. Roles here are responsible for governing access to Wazuh Manager's core functions, like managing agents(Register, remove, restart or even view them), configure specific rules, decoders and CDB lists under ```Server Management```, access and modify server configurations, trigger active response actions, view server logs. If, for example, you only configure internal roles, assign users to that role, and login with them, you might notice that you will not be able to perform the tasks related to above mentioned settings. So, if you are creating a user(internal user), you must not forget to configure settings in ```Server Management > Roles mapping as well```. 

Wazuh has 2 types of users: __indexer__(also known as internal users), and __API__. Indexer users query logs, manage indices, and interact with Wazuh Dashboard. So, when we want to configure a user for being able to login to wazuh via dashboard, we must configure these users.
By default, 2 indexer users are created: _admin_ and _kibanauser_. In my case, because I have deployed wazuh with docker installation method, their passwords are configurable from the docker-compose file.\
We can create indexer users by going: ```Wazuh Dashboard Settings > Security > Internal Users > Create Internal User```.
***Pro tip:*** I advice to use only the following special characters: ```. * + ? -```, because it can create some issues Filebeat. Passwords should be normally 8 to 64 characters logs, have a number and a special character.
