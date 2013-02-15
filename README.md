Airbrake Java
=============

This is the notifier jar for integrating apps with [Airbrake](http://airbrake.io) with Java Applications. Sign up for a [Free](https://airbrake.io/account/new/Free) or [Paid](https://airbrake.io/account/new?source=github) account.

When an uncaught exception occurs, Airbrake will POST the relevant data
to the Airbrake server specified in your environment.

The easy way to use airbrake is configuriong log4j appender. Otherwise if you don't 
use log4j you can use airbrake notifier directly with a very simple API.

Setting up with Maven
---------------------

	<dependency>
		<groupId>io.airbrake</groupId>
		<artifactId>airbrake-java</artifactId>
		<version>2.3.0/version>
	</dependency>


Without Maven
-------------

you need to add these libraries to your classpath
 * [airbrake-java-2.3.0](https://github.com/airbrake/airbrake-java/blob/master/maven2/io/airbrake/airbrake-java/2.3.0/airbrake-java-2.3.0.jar?raw=true)
 * [log4j-1.2.14](https://github.com/airbrake/airbrake-java/blob/master/maven2/log4j/1.2.14/log4j-1.2.14.jar?raw=true)


Howto use it
------------------------------

	Airbrake airbrake = new Airbrake(YOUR_AIRBRAKE_PROJECT_ID, YOUR_AIRBRAKE_PROJECT_ID);

	try {
		throw new RuntimeException("example1");
	} catch (Exception e) {
		airbrake.notify(e);
	}



Howto use it with Log4J
-----------------------

	log4j.rootLogger=ERROR, airbrake

	log4j.appender.airbrake=io.airbrake.AirbrakeAppender
	log4j.appender.airbrake.enabled=true
	log4j.appender.airbrake.url=http://collect.airbrake.io
	log4j.appender.airbrake.apiKey=YOUR_AIRBRAKE_API_KEY
	log4j.appender.airbrake.projectId=YOUR_AIRBRAKE_PROJECT_ID
	log4j.appender.airbrake.envName=test
	log4j.appender.airbrake.appVersion=1.0

or in XML format:

	<appender name="AIRBRAKE" class="airbrake.AirbrakeAppender">
		<param name="enabled" value="true"/>
		<param name="url" value="http://collect.airbrake.io"/>
		<param name="apiKey" value="YOUR_AIRBRAKE_API_KEY"/>
		<param name="projectId" value="YOUR_AIRBRAKE_PROJECT_ID"/>
		<param name="envName" value="test"/>
		<param name="appVersion" value="1.0"/>
	</appender>

	<root>
		<appender-ref ref="AIRBRAKE"/>
	</root>


Howto use it with Filter
------------------------

	public class AirbrakeFilter implements Filter {

		private Airbrake airbrake;

		@Override
		public void init(FilterConfig filterConfig) throws ServletException {

			airbrake = new Airbrake(YOUR_AIRBRAKE_PROJECT_ID, YOUR_AIRBRAKE_PROJECT_ID) {{

				version("1.0");

				// to collect inforation about your environment
				environment("test");

				// to ignore sensitive environment informations
				environmentFilter("AWS_SECRET");
				environmentFilter("EC2_PRIVATE_KEY");


				// to remove stacktrace line that are not useful
				stacktraceFilter("java.awt.*");
				stacktraceFilter("java.vendor.*");
				stacktraceFilter("java.class.path");
				stacktraceFilter("java.vm.specification.*");

				// to replace sensitive information sent to the Airbrake service with [FILTERED]
				paramFilter("creditCardNumber");
			}};
		}

		@Override
		public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

			try {
				chain.doFilter(request, response);
			} catch (Exception e) {
				airbrake.notify(e, new HashMap(), request, System.getProperties());
			}
		}
	
		@Override
		public void destroy() {}

	}


Setup different endpoint
------------------------

	new Airbrake(YOUR_AIRBRAKE_PROJECT_ID, YOUR_AIRBRAKE_PROJECT_ID, "http://collect.airbrake.io");




Support
-------

For help with using Airbrake and this notifier visit [our support site](http://help.airbrake.io).

For SSL verification see the [Resources](https://github.com/airbrake/airbrake/blob/master/resources/README.md).

For any issues, please post then in our [Issues](https://github.com/airbrake/airbrake-java/issues).


