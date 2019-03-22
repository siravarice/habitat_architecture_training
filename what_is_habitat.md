# What is Habitat?

Chef Habitat is a one-year-old application configuration management project, designed to extend in a different direction from Chef's main focus by separating the application from infrastructure configuration.

Tools such as Chef, Puppet and Ansible look at configuration management from the perspective of the managed server's desired state. Habitat instead works against applications and containers wherein administrators push specific parameters to specific applications.

## Depend on configuration management

Habitat takes package management out of configuration management tools. It's easier to run apps in production when administrators can separate configuration management and application management. This reduces complexity in the stack for administrators. Habitat is best compared to a package manager -- with the addition of manageable scripting within the package.

Packages have specific dependencies that are a part of their requirements; these describe what is required to run specific software. Habitat can isolate package dependencies to run on bare metal, VMs or cloud environments. It makes sense to distribute dependencies with the package and not to manage dependencies with other configurations for servers and infrastructure.

Habitat handles parameters along with dependencies. The application configuration management tool's parameter features control the application and host OS configurations in rapidly changing deployments. Parameters could include how much RAM the application can use, the number of processes to spawn and other assignments.

## Make yourself at home in Habitat
Administrators query their applications via Chef Habitat's command-line interface. Based on this query, some output is shown about the command's current parameters. Administrators may start the same application with alternative parameters once only using the hab command. They can also use hab to make persistent changes to the configuration that an application uses. Habitat uses TOML files for this application configuration management task.

The Habitat supervisor creates the TOML file. The TOML file specifies which parameters can be modified. After modifying a TOML file, the new configuration may be pushed to the application prior to launch. Habitat service groups push the configuration to the nodes. A Habitat supervisor defines the service group, which allows users to run a command against any single node and, subsequently, automatically replicate the action to all nodes within the group.

Administrators who adopt Chef Habitat must gain expertise in service group management. Habitat starts a service by automatically running it in a service group with a standalone topology. An administrator extends that topology to include other nodes; the Habitat supervisor declares the leader of the group, and the other nodes in the service group join the group accordingly.

## Living separately
Chef developed Habitat, but integrating the application configuration management tool with Chef's primary infrastructure configuration technology is anything but smooth. There are two primary reasons why this integration is lacking: To date, the creators have focused on Habitat's own development, and Chef and Habitat take aim at two different problems. Habitat focuses on applications, which are dynamic. Chef focuses on server configurations, which are static. These aren't exclusive tasks, however, and administrators in DevOps and traditional IT shops are watching to see how Chef handles the two roadmaps.
