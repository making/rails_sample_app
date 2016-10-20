# Ruby on Rails Tutorial: sample application

This is the sample application for [*Ruby on Rails Tutorial: Learn Rails by Example*](http://railstutorial.org/) by [Michael Hartl](http://michaelhartl.com/).

This sample has been modified to run on Cloud Foundry. The `cf-autoconfig` gem was added to enable auto-configuration of database connections as described in the [Cloud Foundry documentation](http://docs.cloudfoundry.com/docs/using/services/ruby-service-bindings.html). The `pg` gem was also added to support connection to PostgreSQL database. 

## Running the application on Cloud Foundry

After installing in the 'cf' [command-line interface for Cloud Foundry](http://docs.cloudfoundry.org/devguide/installcf/),
targeting a Cloud Foundry instance, and logging in, the application can be pushed using these commands:

First, view a list of services and plans available in your Cloud Foundry instance: 

~~~
$ cf marketplace
Getting services from marketplace
OK

service          plans                                                                 description
blazemeter       free-tier, basic1kmr, pro5kmr, pp10kmr, hv40kmr                       The JMeter Load Testing Cloud
cleardb          spark, boost, amp, shock                                              Highly available MySQL for your Apps.
cloudamqp        lemur, tiger, bunny, rabbit, panda                                    Managed HA RabbitMQ servers in the cloud
cloudforge       free, standard, pro                                                   Development Tools In The Cloud
elephantsql      turtle, panda, hippo, elephant                                        PostgreSQL as a Service
ironmq           pro_platinum, pro_gold, large, medium, small, pro_silver              Powerful Durable Message Queueing Service
ironworker       large, pro_gold, pro_platinum, pro_silver, small, medium              Scalable Background and Async Processing
loadimpact       lifree, li100, li500, li1000                                          Automated and on-demand performance testing
memcachedcloud   25mb, 100mb, 250mb, 500mb, 1gb, 2-5gb, 5gb                            Enterprise-Class Memcached for Developers
mongolab         sandbox                                                               Fully-managed MongoDB-as-a-Service
newrelic         standard                                                              Manage and monitor your apps
rediscloud       25mb, 100mb, 250mb, 500mb, 1gb, 2-5gb, 5gb, 10gb, 50gb                Enterprise-Class Redis for Developers
searchify        small, plus, pro                                                      Custom search you control
searchly         small, micro, professional, advanced, starter, business, enterprise   Search Made Simple. Powered-by ElasticSearch
sendgrid         free, bronze, silver, gold, platinum                                  Email Delivery. Simplified.
~~~

Choose a PostgreSQL service from the list and create a service instance named `rails-postgres` using a PostgreSQL service and plan: 

~~~
$ cf create-service elephantsql turtle rails-postgres
Creating service rails-postgres
OK
~~~

Now push the application: 

~~~
$ cf push
~~~

The application will be pushed using settings in the provided `manifest.yml` file. The output of the
`cf push` command shows the URL that was assigned. Using the provided URL you can browse to the running application.

Then, you need to run db migration script. You can use [v3-cli-plugin](https://github.com/cloudfoundry/v3-cli-plugin).

~~~
$ cf v3-run-task rails-sample migration 'export DATABASE_URL=`echo $VCAP_SERVICES | jq -r .elephantsql[0].credentials.uri` && bundle exec rake db:migrate'
OK

Running task migration on app rails-sample...

Tailing logs for app rails-sample...

2016-10-20T15:31:33.79+0900 [APP/TASK/migration/0]OUT Creating container
2016-10-20T15:31:40.68+0900 [APP/TASK/migration/0]OUT Successfully created container
2016-10-20T15:31:45.16+0900 [APP/TASK/migration/0]OUT Migrating to CreateUsers (20100821203213)
2016-10-20T15:31:45.16+0900 [APP/TASK/migration/0]OUT ==  CreateUsers: migrating ====================================================
2016-10-20T15:31:45.16+0900 [APP/TASK/migration/0]OUT -- create_table(:users)
2016-10-20T15:31:45.17+0900 [APP/TASK/migration/0]OUT    -> 0.0102s
2016-10-20T15:31:45.17+0900 [APP/TASK/migration/0]OUT ==  CreateUsers: migrated (0.0103s) ===========================================
2016-10-20T15:31:45.19+0900 [APP/TASK/migration/0]OUT Migrating to AddEmailUniquenessIndex (20100822012431)
2016-10-20T15:31:45.19+0900 [APP/TASK/migration/0]OUT ==  AddEmailUniquenessIndex: migrating ========================================
2016-10-20T15:31:45.19+0900 [APP/TASK/migration/0]OUT -- add_index(:users, :email, {:unique=>true})
2016-10-20T15:31:45.20+0900 [APP/TASK/migration/0]OUT    -> 0.0076s
2016-10-20T15:31:45.20+0900 [APP/TASK/migration/0]OUT ==  AddEmailUniquenessIndex: migrated (0.0077s) ===============================
2016-10-20T15:31:45.21+0900 [APP/TASK/migration/0]OUT Migrating to AddPasswordToUsers (20100822204528)
2016-10-20T15:31:45.21+0900 [APP/TASK/migration/0]OUT -- add_column(:users, :encrypted_password, :string)
2016-10-20T15:31:45.21+0900 [APP/TASK/migration/0]OUT    -> 0.0012s
2016-10-20T15:31:45.21+0900 [APP/TASK/migration/0]OUT ==  AddPasswordToUsers: migrated (0.0014s) ====================================
2016-10-20T15:31:45.23+0900 [APP/TASK/migration/0]OUT Migrating to AddSaltToUsers (20100822233125)
2016-10-20T15:31:45.23+0900 [APP/TASK/migration/0]OUT ==  AddSaltToUsers: migrating =================================================
2016-10-20T15:31:45.23+0900 [APP/TASK/migration/0]OUT -- add_column(:users, :salt, :string)
2016-10-20T15:31:45.23+0900 [APP/TASK/migration/0]OUT    -> 0.0012s
2016-10-20T15:31:45.21+0900 [APP/TASK/migration/0]OUT ==  AddPasswordToUsers: migrating =============================================
2016-10-20T15:31:45.23+0900 [APP/TASK/migration/0]OUT ==  AddSaltToUsers: migrated (0.0013s) ========================================
2016-10-20T15:31:45.24+0900 [APP/TASK/migration/0]OUT Migrating to AddAdminToUsers (20100829021049)
2016-10-20T15:31:45.24+0900 [APP/TASK/migration/0]OUT ==  AddAdminToUsers: migrating ================================================
2016-10-20T15:31:45.24+0900 [APP/TASK/migration/0]OUT -- add_column(:users, :admin, :boolean, {:default=>false})
2016-10-20T15:31:45.25+0900 [APP/TASK/migration/0]OUT ==  AddAdminToUsers: migrated (0.0047s) =======================================
2016-10-20T15:31:45.26+0900 [APP/TASK/migration/0]OUT Migrating to CreateMicroposts (20100829210229)
2016-10-20T15:31:45.26+0900 [APP/TASK/migration/0]OUT ==  CreateMicroposts: migrating ===============================================
2016-10-20T15:31:45.26+0900 [APP/TASK/migration/0]OUT -- create_table(:microposts)
2016-10-20T15:31:45.27+0900 [APP/TASK/migration/0]OUT    -> 0.0058s
2016-10-20T15:31:45.27+0900 [APP/TASK/migration/0]OUT -- add_index(:microposts, [:user_id, :created_at])
2016-10-20T15:31:45.28+0900 [APP/TASK/migration/0]OUT    -> 0.0062s
2016-10-20T15:31:45.28+0900 [APP/TASK/migration/0]OUT ==  CreateMicroposts: migrated (0.0122s) ======================================
2016-10-20T15:31:45.29+0900 [APP/TASK/migration/0]OUT Migrating to CreateRelationships (20100831012055)
2016-10-20T15:31:45.29+0900 [APP/TASK/migration/0]OUT ==  CreateRelationships: migrating ============================================
2016-10-20T15:31:45.29+0900 [APP/TASK/migration/0]OUT -- create_table(:relationships)
2016-10-20T15:31:45.30+0900 [APP/TASK/migration/0]OUT    -> 0.0067s
2016-10-20T15:31:45.30+0900 [APP/TASK/migration/0]OUT -- add_index(:relationships, :follower_id)
2016-10-20T15:31:45.31+0900 [APP/TASK/migration/0]OUT    -> 0.0060s
2016-10-20T15:31:45.31+0900 [APP/TASK/migration/0]OUT -- add_index(:relationships, :followed_id)
2016-10-20T15:31:45.31+0900 [APP/TASK/migration/0]OUT    -> 0.0059s
2016-10-20T15:31:45.31+0900 [APP/TASK/migration/0]OUT -- add_index(:relationships, [:follower_id, :followed_id], {:unique=>true})
2016-10-20T15:31:45.32+0900 [APP/TASK/migration/0]OUT    -> 0.0059s
2016-10-20T15:31:45.32+0900 [APP/TASK/migration/0]OUT ==  CreateRelationships: migrated (0.0249s) ===================================
2016-10-20T15:31:45.25+0900 [APP/TASK/migration/0]OUT    -> 0.0046s
2016-10-20T15:31:45.44+0900 [APP/TASK/migration/0]OUT Exit status 0
2016-10-20T15:31:45.55+0900 [APP/TASK/migration/0]OUT Destroying container
Task aed5684d-ddee-452b-9d9c-b416c09aa735 successfully completed.
~~~

Because the migration has been done, you 2nd `cf v3-run-task` changes nothing as follows:

~~~
$ cf v3-run-task rails-sample migration 'export DATABASE_URL=`echo $VCAP_SERVICES | jq -r .elephantsql[0].credentials.uri` && bundle exec rake db:migrate'
OK

Running task migration on app rails-sample...

Tailing logs for app rails-sample...

2016-10-20T15:31:54.49+0900 [APP/TASK/migration/0]OUT Creating container
2016-10-20T15:32:01.63+0900 [APP/TASK/migration/0]OUT Successfully created container
2016-10-20T15:32:05.17+0900 [APP/TASK/migration/0]OUT Migrating to CreateUsers (20100821203213)
2016-10-20T15:32:05.17+0900 [APP/TASK/migration/0]OUT Migrating to AddEmailUniquenessIndex (20100822012431)
2016-10-20T15:32:05.17+0900 [APP/TASK/migration/0]OUT Migrating to AddPasswordToUsers (20100822204528)
2016-10-20T15:32:05.17+0900 [APP/TASK/migration/0]OUT Migrating to AddSaltToUsers (20100822233125)
2016-10-20T15:32:05.17+0900 [APP/TASK/migration/0]OUT Migrating to AddAdminToUsers (20100829021049)
2016-10-20T15:32:05.17+0900 [APP/TASK/migration/0]OUT Migrating to CreateMicroposts (20100829210229)
2016-10-20T15:32:05.17+0900 [APP/TASK/migration/0]OUT Migrating to CreateRelationships (20100831012055)
2016-10-20T15:32:05.23+0900 [APP/TASK/migration/0]OUT Exit status 0
2016-10-20T15:32:05.33+0900 [APP/TASK/migration/0]OUT Destroying container
Task 39cece9f-855d-4f34-aea2-5174343b0fd6 successfully completed.
~~~
