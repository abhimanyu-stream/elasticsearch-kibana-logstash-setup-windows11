# elasticsearch-kibana-logstash-setup-windows11


# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html




#USE_BELOW_COMMAND_TO_CREATE_A_USER_PASSWORD_FOR_LOGIN
#bin/elasticsearch-users useradd PUT_USER_NAME_HERE -p PUT_PASSWORD_FOR_THIS_USER -r superuser
#all specified roles:-
#Known roles:[apm_system, watcher_admin, viewer, rollup_user, logstash_system, kibana_user, beats_admin, remote_monitoring_agent, rollup_admin, data_frame_transforms_admin, snapshot_user, monitoring_user, enrich_user, kibana_admin, logstash_admin, editor, inference_user, machine_learning_user, data_frame_transforms_user, machine_learning_admin, watcher_user, apm_user, inference_admin, beats_system, reporting_user, transform_user, kibana_system, transform_admin, transport_client, remote_monitoring_collector, ingest_admin, superuser]

#Example_To_Create_User
#C:\elasticsearch-8.15.0\bin>elasticsearch-users useradd acode -p acode123 -r superuser
#warning: ignoring JAVA_HOME=E:\Installation\Java\jdk17.0.10; using ES_JAVA_HOME


Setting up Elasticsearch 8 on Windows 11 involves several steps, including installation, configuration, and setting up security with a keystore file (keystore.p12). Below is a step-by-step guide:

Step 1: Prerequisites
Java Installation: Elasticsearch 8 comes bundled with OpenJDK, so you don't need to install Java separately.

Hardware Requirements: Ensure your system meets the minimum hardware requirements:

RAM: Minimum 4GB, recommended 8GB.
Disk Space: At least 10GB free space.
CPU: A recent Intel or AMD processor.
Step 2: Download Elasticsearch
Download the Elasticsearch 8 ZIP Archive:

Visit the Elasticsearch download page.
Choose the Windows version and download the ZIP archive.
Extract the ZIP File:

Extract the downloaded ZIP file to a directory, e.g., C:\elasticsearch-8.15.0
Step 3: Configuring Elasticsearch
Navigate to the Configuration Directory:

Go to the directory where you extracted Elasticsearch: C:\elasticsearch-8.15.0\config.
Edit elasticsearch.yml:

Open elasticsearch.yml in a text editor.

Update the following basic settings:

#####elasticsearch.yml#####
#####OnWindows11Machine#####

cluster.name: stream-cluster
node.name: node-1
path.data: 'E:\Elasticsearch\data'
path.logs: 'E:\Elasticsearch\logs'
network.host: 0.0.0.0
http.port: 9200

cluster.initial_master_nodes:
- node-1

xpack.security.authc.realms.native.native1:
order: 0
xpack.security.enabled: true
xpack.security.enrollment.enabled: true
xpack.security.http.ssl:
enabled: true
verification_mode: certificate
keystore.type: PKCS12
keystore.path: 'C:\\elasticsearch-8.15.0\\config\\keystore.p12'
keystore.password: "abhimanyu"



xpack.security.transport.ssl:
enabled: true
verification_mode: certificate
client_authentication: required
keystore.type: PKCS12
keystore.path: 'C:\\elasticsearch-8.15.0\\config\\keystore.p12'
keystore.password: "abhimanyu"




#This configuration sets up a single-node cluster with data and log paths defined. The network host 0.0.0.0 allows Elasticsearch to be accessed from any IP address on your network.

Step 4: Setting Up Keystore for Security
Generate a PKCS#12 Keystore:

If you don't already have a .p12 keystore, you can create one using OpenSSL:

Generate a Certificate
Before adding passwords to the Elasticsearch keystore, you need to generate a certificate. You can use the Elasticsearch certutil tool to generate a self-signed certificate. Here’s how to do it:

bin/elasticsearch-certutil cert --name keystore --days 365 --self-signed

C:\elasticsearch-8.15.0\bin>elasticsearch-certutil cert --name keystore --days 365 --self-signed
warning: ignoring JAVA_HOME=E:\Installation\Java\jdk17.0.10; using ES_JAVA_HOME
This tool assists you in the generation of X.509 certificates and certificate
signing requests for use with SSL/TLS in the Elastic stack.

The 'cert' mode generates X.509 certificate and private keys.
* By default, this generates a single certificate and key for use
on a single instance.
* The '-multiple' option will prompt you to enter details for multiple
instances and will generate a certificate and key for each one
* The '-in' option allows for the certificate generation to be automated by describing
the details of each instance in a YAML file

    * An instance is any piece of the Elastic Stack that requires an SSL certificate.
      Depending on your configuration, Elasticsearch, Logstash, Kibana, and Beats
      may all require a certificate and private key.
    * The minimum required value for each instance is a name. This can simply be the
      hostname, which will be used as the Common Name of the certificate. A full
      distinguished name may also be used.
    * A filename value may be required for each instance. This is necessary when the
      name would result in an invalid file or directory name. The name provided here
      is used as the directory name (within the zip) and the prefix for the key and
      certificate files. The filename is required if you are prompted and the name
      is not displayed in the prompt.
    * IP addresses and DNS names are optional. Multiple values can be specified as a
      comma separated string. If no IP addresses or DNS names are provided, you may
      disable hostname verification in your SSL configuration.


    * All certificates generated by this tool will be signed by a certificate authority (CA)
      unless the --self-signed command line option is specified.
      The tool can automatically generate a new CA for you, or you can provide your own with
      the --ca or --ca-cert command line options.


By default the 'cert' mode produces a single PKCS#12 output file which holds:
* The instance certificate
* The private key for the instance certificate
* The CA certificate

If you specify any of the following options:
* -pem (PEM formatted output)
* -multiple (generate multiple certificates)
* -in (generate certificates from an input file)
then the output will be be a zip file containing individual certificate/key files

#NOTE:- BELOW LINE WILL PROMPT FOR NAME FOR KEYSTORE FILE WITH EXT, TYPE keystore.p12

Please enter the desired output file [keystore.p12]: keystore.p12
Enter password for keystore.p12 :TYPE_YOUR_PASSWORD_HERE

Certificates written to C:\elasticsearch-8.15.0\keystore.p12

This file should be properly secured as it contains the private key for your instance.
This file is a self contained file and can be copied and used 'as is'
For each Elastic product that you wish to configure, you should copy
this '.p12' file to the relevant configuration directory
and then follow the SSL configuration instructions in the product guide.

C:\elasticsearch-8.15.0\bin>

Copy keystore.p12 to Elasticsearch Config Directory:
#LIKE:-   C:\elasticsearch-8.15.0\config

Place the keystore.p12 file in C:\elasticsearch-8.15.0\config.
Edit elasticsearch.yml for TLS/SSL:

#note:-  ALL_DONE_ABOVE_FOR_YOU_ONLY_REPLACE_KEYSTORE_PASSWORD

Configure User Authentication (Optional but recommended):

Elasticsearch has a built-in user authentication mechanism. Enable it by adding the following lines:

xpack.security.authc.realms.native.native1:
order: 0

#NOTE:- ALREADY_DONE_ABOVE

Step 5: Start Elasticsearch
Run Elasticsearch:
Open a Command Prompt as Administrator.

Navigate to the Elasticsearch bin directory:


cd C:\elasticsearch-8.15.0\bin
Start Elasticsearch:


elasticsearch.bat
Elasticsearch will start up and should be accessible via https://localhost:9200 (since SSL/TLS is enabled).

#SIGN IN VIA A USER CREATED BY COMMAND
#Example_To_Create_User
#C:\elasticsearch-8.15.0\bin>elasticsearch-users useradd acode -p acode123 -r superuser

Step 6: Verify Installation
Test the Installation:

Open a web browser and navigate to https://localhost:9200.

You should see a response similar to:

json
Copy code
{
"name" : "node-1",
"cluster_name" : "my-cluster",
"cluster_uuid" : "xxxxxxxxxxxxxx",
"version" : {
"number" : "8.x.x",
"build_flavor" : "default",
"build_type" : "zip",
"build_hash" : "xxxxxxxxxxxxx",
"build_date" : "202x-xx-xxTxx:xx:xx.xxZ",
"build_snapshot" : false,
"lucene_version" : "9.x.x",
"minimum_wire_compatibility_version" : "7.x.x",
"minimum_index_compatibility_version" : "7.x.x"
},
"tagline" : "You Know, for Search"
}
Access with Authentication:

If authentication is enabled, you'll be prompted for credentials. Use the default elastic user and password.
Step 7: Secure the Installation
Create Users:

Use the Elasticsearch bin tools to create users and roles, enhancing security.

bin/elasticsearch-users useradd my_user -p my_password -r superuser
#Example_To_Create_User
#C:\elasticsearch-8.15.0\bin>elasticsearch-users useradd acode -p acode123 -r superuser

Set Up Firewalls:

Ensure Windows Firewall is configured to allow or block traffic to Elasticsearch based on your network requirements.