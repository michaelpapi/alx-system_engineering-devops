Post-Event Analysis

Following the launch of ALX's System Engineering & DevOps project at approximately 06:00 West African Time (WAT) in Nigeria, an outage ensued within an isolated Ubuntu 20.04 container operating an Apache web server. During this time, GET requests to the server resulted in 500 Internal Server Errors, contrary to the expected response of an HTML file defining a basic Holberton WordPress site.

Troubleshooting Process

The issue was first identified by Favour Uko around 19:20 PST, who promptly initiated efforts to resolve it.

1. Examination of running processes using the command "ps aux" revealed two apache2 processes - one under the root user and the other under www-data, indicating proper functionality.

2. Inspection of the sites-available folder within the /etc/apache2/ directory confirmed that the web server was configured to serve content from /var/www/html/.

3. Employing the strace utility on the root Apache process PID provided no significant insights when combined with a curl test.

4. Repeating the strace procedure on the www-data process PID yielded valuable information, specifically an "-1 ENOENT" (No such file or directory) error when attempting to access the file /var/www/html/wp-includes/class-wp-locale.phpp.

5. A meticulous search through files in the /var/www/html/ directory, utilizing Vim pattern matching, pinpointed the erroneous ".phpp" file extension within the wp-settings.php file (Line 137: require_once( ABSPATH . WPINC . '/class-wp-locale.phpp' );).

6. The issue was rectified by removing the extraneous "p" from the file extension.

7. Verification through another curl test confirmed successful resolution with a 200 response.

8. Automation of the error correction process was facilitated through the creation of a Puppet manifest (0-strace_is_your_friend.pp).

Summary

The root cause of the outage was traced to a typographical error within the WordPress application, specifically in wp-settings.php, where an attempt was made to load the file class-wp-locale.phpp instead of class-wp-locale.php, which resides in the wp-content directory.

The remedy involved a straightforward correction of the typo by eliminating the trailing "p."

Preventive Measures

To avert similar incidents in the future, the following precautions are recommended:

1. Thorough testing of the application prior to deployment to identify and address potential errors beforehand.

2. Implementation of status monitoring mechanisms such as UptimeRobot to promptly notify stakeholders of website downtime.

3. Adoption of automation tools like Puppet manifests to streamline the resolution of recurring errors, as demonstrated by 0-strace_is_your_friend.pp, which addresses any identical issues by replacing ".phpp" extensions in wp-settings.php with ".php."
