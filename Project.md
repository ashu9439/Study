```
/**
 * @name scan
 * @description Scans DynamoDB table for getting the records based on filter condition
 * @param  {any} tableName - Name of the table
 * @param  {any} filterExpression - The filter expression to use
 * @param  {any} expressionAttribNames - Name of the attributes in filter expression
 * @param  {any} expressionAttribValues - value of attributes in filter expression
 * @param  {any} projectExpression - Projection expression
 * @returns Promise
 */
export async function scan(tableName: any, filterExpression: any, expressionAttribNames: any,
    expressionAttribValues: any, projectExpression: any): Promise<any> {
    try {
        log.info('Region:', process.env.REGION);
        AWS.config.update({ region: process.env.REGION });

        const db = new AWS.DynamoDB.DocumentClient();
        const dbParams: AWS.DynamoDB.DocumentClient.ScanInput = {
            TableName: tableName,
            FilterExpression: filterExpression,
            ExpressionAttributeNames: expressionAttribNames,
            ExpressionAttributeValues: expressionAttribValues,
            ProjectionExpression: projectExpression
        };
        let scannedResult: any[] = [];
        let dbPromise = db.scan(dbParams).promise();
        let response = await dbPromise;
        if (!_.isEmpty(response)) {
            if (!_.isEmpty(response.LastEvaluatedKey)) {
                if (!_.isEmpty(response.Items)) {
                    response.Items.forEach(item => {
                        scannedResult.push(item);
                    });
                }
                while (!_.isEmpty(response.LastEvaluatedKey)) {
                    dbParams.ExclusiveStartKey = response.LastEvaluatedKey;
                    response = await db.scan(dbParams).promise();
                    if (!_.isEmpty(response.Items)) {
                        response.Items.forEach(item => {
                            scannedResult.push(item);
                        });
                    }
                }
                return Promise.resolve(scannedResult);
            } else {
                return Promise.resolve(response.Items);
            }
        } else {
            return Promise.resolve([]);
        }
    } catch (error) {
        log.error('Error Connecting to DynamoDB', error.stack);
        throw error;
    }
}

/**
 * @name scan
 * @description Scans DynamoDB table for getting all the records
 * @param  {any} tableName - Name of the table
 * @param  {any} projectExpression - Projection expression
 * @returns Promise
 */
export async function fullScan(tableName: any): Promise<any> {
    AWS.config.update({ region: process.env.REGION });
    const db = new AWS.DynamoDB.DocumentClient();
    try {

        const dbParams: AWS.DynamoDB.DocumentClient.ScanInput = {
            TableName: tableName
        };
        let scannedResult: any[] = [];
        let dbPromise = db.scan(dbParams).promise();
        let response = await dbPromise;
        if (!_.isEmpty(response)) {
            if (!_.isEmpty(response.LastEvaluatedKey)) {
                if (!_.isEmpty(response.Items)) {
                    response.Items.forEach(item => {
                        scannedResult.push(item);
                    });
                }
                while (!_.isEmpty(response.LastEvaluatedKey)) {
                    dbParams.ExclusiveStartKey = response.LastEvaluatedKey;
                    response = await db.scan(dbParams).promise();
                    if (!_.isEmpty(response.Items)) {
                        response.Items.forEach(item => {
                            scannedResult.push(item);
                        });
                    }
                }
                return Promise.resolve(scannedResult);
            } else {
                return Promise.resolve(response.Items);
            }
        } else {
            return Promise.resolve([]);
        }
    } catch (error) {
        log.error('Error Connecting to DynamoDB', error.stack);
        throw error;
    }
}

/**
 * @name scan
 * @description Scans DynamoDB table for getting the records based on filter condition
 * @param  {any} tableName - Name of the table
 * @param  {any} indexName - Index Name of the table
 * @param  {any} filterExpression - The filter expression to use
 * @param  {any} expressionAttribNames - Name of the attributes in filter expression
 * @param  {any} expressionAttribValues - value of attributes in filter expression
 * @param  {any} projectExpression - Projection expression
 * @returns Promise
 */
export async function scanIndex(tableName: any, indexName: string, filterExpression: any,
    expressionAttribNames: any, expressionAttribValues: any,
    projectExpression: any): Promise<any[]> {
    try {
        const db = new AWS.DynamoDB.DocumentClient();
        const dbParams: AWS.DynamoDB.DocumentClient.ScanInput = {
            TableName: tableName,
            IndexName: indexName,
            FilterExpression: filterExpression,
            ExpressionAttributeNames: expressionAttribNames,
            ExpressionAttributeValues: expressionAttribValues,
            ProjectionExpression: projectExpression
        };
        let scannedResult: any[] = [];
        let dbPromise = db.scan(dbParams).promise();
        let response = await dbPromise;
        if (!_.isEmpty(response)) {
            if (!_.isEmpty(response.LastEvaluatedKey)) {
                if (!_.isEmpty(response.Items)) {
                    response.Items.forEach(item => {
                        scannedResult.push(item);
                    });
                }
                while (!_.isEmpty(response.LastEvaluatedKey)) {
                    dbParams.ExclusiveStartKey = response.LastEvaluatedKey;
                    response = await db.scan(dbParams).promise();
                    if (!_.isEmpty(response.Items)) {
                        response.Items.forEach(item => {
                            scannedResult.push(item);
                        });
                    }
                }
                return Promise.resolve(scannedResult);
            } else {
                return Promise.resolve(response.Items);
            }
        } else {
            return Promise.resolve([]);
        }
    } catch (error) {
        log.error({ error }, 'Error Connecting to DynamoDB inside scan');
        throw error;
    }
}

export async function query(tableName: any, indexName: any, keyCondExpression: any, filterExpression: any,
    expressionAttribNames: any, expressionAttribValues: any,
    projectExpression): Promise<any> {
    try {
        AWS.config.update({ region: process.env.REGION });
        const db = new AWS.DynamoDB.DocumentClient();
        let queriedResult: any[] = [];
        const dbParams: AWS.DynamoDB.DocumentClient.QueryInput = {
            TableName: tableName,
            IndexName: indexName,
            FilterExpression: filterExpression,
            ProjectionExpression: projectExpression,
            ExpressionAttributeNames: expressionAttribNames,
            ExpressionAttributeValues: expressionAttribValues,
            KeyConditionExpression: keyCondExpression
        };

        let dbPromise = db.query(dbParams).promise();
        let response = await dbPromise;
        if (!_.isEmpty(response)) {
            if (!_.isEmpty(response.LastEvaluatedKey)) {
                if (!_.isEmpty(response.Items)) {
                    response.Items.forEach(item => {
                        queriedResult.push(item);
                    });
                }
                while (!_.isEmpty(response.LastEvaluatedKey)) {
                    dbParams.ExclusiveStartKey = response.LastEvaluatedKey;
                    response = await db.query(dbParams).promise();
                    if (!_.isEmpty(response.Items)) {
                        response.Items.forEach(item => {
                            queriedResult.push(item);
                        });
                    }
                }
                return Promise.resolve(queriedResult);
            } else {
                return Promise.resolve(response.Items);
            }
        } else {
            return Promise.resolve([]);
        }

    } catch (error) {
        log.error('Error Connecting to DynamoDB', error.stack);
        throw error;
    }
}

```

























This Spring Integration XML configuration defines a series of steps for processing messages in a repair order system. Here's a breakdown of the key steps:

1. **Initial Validation:**
   - The process starts with a service activator (`repairOrderBusinessValidationService`) performing initial validations on a channel (`businessValidationRequestChannel`).

2. **Business Validation:**
   - The result of initial validation is bridged to another channel (`businessValidationChannel`).
   - There's a commented-out router that could potentially direct messages based on business validation status, but it's currently not active.

3. **Processing Validated Requests:**
   - Successful business-validated requests are bridged to a channel (`processorRequestChannel`).
   - A service activator (`processROReplayServiceActivator`) checks whether PartyReceiverId is empty and sets a boolean in the header.
   - A router (`roReplayRouter`) directs messages based on the value of `ro_replay` in the header.
   - Another router (`roRouter`) determines whether to post the message on Kinesis Stream based on `process_kinesis_stream` in the header.

4. **Kinesis Stream or Message Queue Handling:**
   - If posting to Kinesis Stream is required, a service activator (`processKsServiceActivator`) handles this.
   - Messages are then routed to either the `emfChannel` or an error handling channel (`kinesisErrorHandlingChannel`).
   - A service activator (`roStartoEmfServiceActivator`) maps messages from STAR to EMF.
   - The transformed EMF message is marshaled to XML (`int-xml:marshalling-transformer`).
   - The XML message is logged (`roEmflogServiceActivator`).

5. **RO Replay Handling:**
   - If `process_ro_replay` is true, messages are sent to a JMS template (`ProcessRoReplayChannel`).
   - The header is enriched (`roReplayHeaderEnricher`) before processing the replay data.
   - A service activator (`repairOrderProcessor`) maps STAR to EMF and posts the replay message (`postROReplayMessage`).
   - Messages are then routed to either Kinesis Stream or a response channel based on `process_kinesis_stream`.

6. **Error Handling:**
   - There's an error handling channel (`roErrorHandlingChannel`) for handling scenarios where certain conditions are not met during processing.
   - Another error handling channel (`kinesisErrorHandlingChannel`) is mentioned but currently not active.

7. **Additional Components:**
   - A custom interceptor (`MessageValidationInterceptor`) is defined for message validation using XSDs.
   - A transformer (`ResultToStringTransformer`) is used to convert the result to a string.

8. **Channels:**
   - Multiple publish-subscribe channels (`businessValidationChannel`, `processorRequestSubscribeChannel`, etc.) are defined for message routing.

This configuration is designed for a repair order system, handling validations, processing, and routing messages based on various conditions. The specific behavior may depend on the implementation of the referenced beans and services (`repairOrderBusinessValidationService`, `repairOrderProcessor`, etc.).



******

This Spring Integration configuration XML defines a set of beans and integration components for processing repair orders in a system. Let's break down the key elements:

1. **Namespace Declarations:**
   - The XML file starts with namespace declarations for Spring Beans and various Spring Integration modules (e.g., file, xml, ws, oxm, task, jdbc, stream).

2. **Message Flow:**
   - **Business Validation:**
     - The configuration starts with a service activator that performs initial validations on the repair order. The result is sent to the "businessValidationChannel."
   - A bridge then connects the "businessValidationChannel" to the "businessValidationResponseChannel."

   - **Processing Steps:**
     - The configuration includes a series of steps using service activators, routers, and channels to handle business-validated requests.
     - It checks and processes the VIN checkdigit validation.
     - There are conditional routers based on validation status and other headers.
     - Channels like "processorRequestChannel," "processorRequestSubscribeChannel," and others are involved in routing and processing.

   - **Kinesis Stream and JMS Handling:**
     - There are routers and service activators to determine whether to post messages on a Kinesis stream or send them to a message queue (JMS).
     - A service activator is configured to post messages on a Kinesis Stream.

   - **Error Handling:**
     - There are channels like "roErrorHandlingChannel" and service activators for handling various error scenarios, such as unavailability.

   - **Header Enricher and Gateways:**
     - Header enrichers are used to add headers to messages.
     - A gateway with ID "roReplayGateway" is configured, likely for handling replay-related operations.

   - **Message Validation Interceptor:**
     - A custom MessageValidationInterceptor bean is defined for validating messages against XML schemas.

   - **Channels:**
     - Various publish-subscribe channels and regular channels are defined to facilitate communication between different components.

   - **Beans:**
     - The configuration includes beans for custom interceptors, a ResultToStringTransformer, and a ListFactoryBean.

3. **XSD Validation:**
   - The `MessageValidationInterceptor` bean is configured with XSD paths to validate messages against predefined XML schemas.

4. **Channels:**
   - Multiple channels are defined to facilitate communication between different components of the integration flow.

5. **Beans:**
   - Custom beans, such as `messageValidationInterceptor` and `domToStringTransformer`, are defined to handle specific functionalities.

In summary, this configuration sets up a comprehensive integration flow for processing repair orders, including validation, routing, error handling, and integration with external systems like Kinesis Stream and JMS. The configuration is modular and follows Spring Integration best practices.
****


```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/integration http://www.springframework.org/schema/integration/spring-integration.xsd
		http://www.springframework.org/schema/integration/file http://www.springframework.org/schema/integration/file/spring-integration-file.xsd
		http://www.springframework.org/schema/integration/xml http://www.springframework.org/schema/integration/xml/spring-integration-xml.xsd
		http://www.springframework.org/schema/oxm http://www.springframework.org/schema/oxm/spring-oxm.xsd
		http://www.springframework.org/schema/integration/ws http://www.springframework.org/schema/integration/ws/spring-integration-ws.xsd
		http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd
		http://www.springframework.org/schema/integration/stream http://www.springframework.org/schema/integration/stream/spring-integration-stream.xsd
		http://www.springframework.org/schema/integration/jdbc http://www.springframework.org/schema/integration/jdbc/spring-integration-jdbc.xsd"
	xmlns:int="http://www.springframework.org/schema/integration"
	xmlns:int-file="http://www.springframework.org/schema/integration/file"
	xmlns:int-xml="http://www.springframework.org/schema/integration/xml"
	xmlns:int-ws="http://www.springframework.org/schema/integration/ws"
	xmlns:oxm="http://www.springframework.org/schema/oxm"
	xmlns:task="http://www.springframework.org/schema/task"
	xmlns:int-jdbc="http://www.springframework.org/schema/integration/jdbc"
	xmlns:int-stream="http://www.springframework.org/schema/integration/stream">

	<!-- Message received after BOD validation has to be passed for business 
		validation -->

	<!-- 1. Perform VIN checkdigit validation. -->
	<int:service-activator input-channel="businessValidationRequestChannel" ref="repairOrderBusinessValidationService"
				method="performInitialValidations" output-channel="businessValidationChannel" />


	<!-- 2. If validation fails, forward the message to response creator channel. 
		else proceed with next validation. -->
	<!-- <int:router input-channel="initialValidatedChannel" expression="headers.messageProcessResultHeader.businessValidationStatus"> 
		<int:mapping value="true" channel="businessValidationChannel" /> <int:mapping 
		value="false" channel="responseCreatorChannel" /> </int:router> -->

	<int:bridge input-channel="businessValidationChannel" output-channel="businessValidationResponseChannel" />

	<!-- 3 B. Gateway and chain configuration, which marshalls the message, 
		send the message to queue and returns the response -->
	<!-- <int:gateway id="eigGateway" default-request-channel="sendROChannel" 
		/> -->
	<!-- <int:chain input-channel="businessValidationChannel" output-channel="esbChannel"> 
		<int:service-activator ref="repairOrderProcessor" method="mapStarToEmf" /> 
		<int-xml:marshalling-transformer marshaller="roMarshaller" result-transformer="domToStringTransformer" 
		/> </int:chain> -->

	<!-- For successful business validated requests, send the message for EMF 
		processing and Response creation -->
	<int:bridge input-channel="processorRequestChannel" output-channel="processorRequestSubscribeChannel" />
	
	<!-- 3. Service Activator to check whether PartyRecieverId is empty or not and set boolean in ro_replay header. -->
	<int:service-activator id="processROReplayServiceActivator" ref="repairOrderProcessor" method="processROMessage" input-channel="processorRequestSubscribeChannel"
		output-channel="routeROMessageChannel" />

	<!-- 4. If ro_replay true emf message will be post on JMS or send the message to kinesis Stream  -->
	<int:router id="roReplayRouter" input-channel="routeROMessageChannel" expression="headers['ro_replay']">
		<int:mapping value="true" channel="ProcessRoReplayVerifyChannel" />
		<int:mapping value="false" channel="ProcessRoChannel" />
	</int:router>
	
	<!-- 4.A If process_kinesis_stream true message will be post on KS and if false message will be send to MQ -->
	<int:router id="roRouter" input-channel="ProcessRoChannel" expression="headers['process_kinesis_stream']">
		<int:mapping value="true" channel="processKSChannel" />
		<int:mapping value="false" channel="emfChannel" />
	</int:router>
	
	<!-- 5. Service Activator to post message on kinesis Stream. -->
	<int:service-activator id="processKsServiceActivator" ref="repairOrderProcessor" method="processKinesisStream" input-channel="processKSChannel"
		output-channel="emfChannel" />

	<!-- 6. Route the message to emfChannel or send it to error channel  -->
	<!-- <int:router id="roEmfRouter" input-channel="processedKSChannel" expression="headers['ro_message_ks_stram_status']">
		<int:mapping value="success" channel="emfChannel" />
		<int:mapping value="failure" channel="kinesisErrorHandlingChannel" />
	</int:router> -->
	
	<!-- 6.A. Service Activator to convert message from STAR to EMF . -->
	<int:service-activator id="roStartoEmfServiceActivator" ref="repairOrderProcessor" method="mapStarToEmf" input-channel="emfChannel" output-channel="emfTransformedChannel" />

	<!-- 6.B. Marshallar to convert the emf object to XML . -->

	<int-xml:marshalling-transformer marshaller="roMarshaller" result-transformer="domToStringTransformer" input-channel="emfTransformedChannel" output-channel="emfPayloadlogChannel" />
	
	<!-- 6.B.1 Service Activator to log emf message  . -->
	 <int:service-activator id="roEmflogServiceActivator" ref="repairOrderProcessor" method="logEmfPayload" input-channel="emfPayloadlogChannel" output-channel="esbChannel" /> 

	
	<!-- 7.A If process_ro_replay is true message will send to MQ and if is false will send to error handling channel  . -->
	<int:router id="roReplyVerifyRouter" input-channel="ProcessRoReplayVerifyChannel" expression="headers['process_ro_replay']">
		<int:mapping value="true" channel="ProcessRoReplayChannel" />
		<int:mapping value="false" channel="processROReplayVerifyKSChannel" />
		<int:mapping value="" channel="roErrorHandlingChannel" />
	</int:router>
	
	<!-- 7.B Send ROReplay Message to JMSTemplate . -->
	<int:header-enricher id="roReplayHeaderEnricher" input-channel="ProcessRoReplayChannel" output-channel="processROReplayVerifyKSChannel">
		<int:header name="roReplayCheckFlag" expression="@roReplayGateway.exchange(#root).payload" />
	</int:header-enricher>
    <int:gateway id="roReplayGateway" default-request-channel="roReplayDataChannel" />
    <!-- 7.C Service Activator for StartToEMF & postROReplayMessage. -->
    <int:chain input-channel="roReplayDataChannel">
		<int:service-activator ref="repairOrderProcessor" method="mapStarToEmf" />
		<int-xml:marshalling-transformer marshaller="roMarshaller" result-transformer="domToStringTransformer" />
		<int:service-activator ref="repairOrderProcessor" method="postROReplayMessage" />
   </int:chain>
   
   <!-- 7.D If process_kinesis_stream is true message will send to KS stream and if is false will send to response channel  -->
   <int:router id="roReplyKSRouter" input-channel="processROReplayVerifyKSChannel" expression="headers['process_kinesis_stream']">
		<int:mapping value="true" channel="processROReplayKSChannel" />
		<int:mapping value="false" channel="responseCreatorChannel" />
	</int:router>
	
	<!-- 7.E. Service Activator to post message on kinesis Stream. -->
    <int:service-activator id="processRoKsServiceActivator" ref="repairOrderProcessor" method="processKinesisStream" input-channel="processROReplayKSChannel"
		output-channel="responseCreatorChannel" />

	<!-- 8.A. Service Activator to error Unavailable. -->
	<int:service-activator id="handleUnavailServiceActivator" input-channel="roErrorHandlingChannel" ref="repairOrderProcessor" method="handleRoUnavailability" />

	<!-- 8.B . Send the message response creator channel -->
	<int:bridge input-channel="processorRequestSubscribeChannel" output-channel="responseCreatorChannel" />

	<bean id="messageValidationInterceptor"
		class="com.tms.ddoa.core.interceptor.MessageValidationInterceptor">
		<property name="xsdList">
			<bean
				class="org.springframework.beans.factory.config.ListFactoryBean">
				<property name="targetListClass">
					<value>java.util.ArrayList</value>
				</property>
				<property name="sourceList">
					<list>
						<value>${xsd.path}/envelope.xsd</value>
						<value>${xsd.path}/BODs/Standalone/ProcessRepairOrder.xsd</value>
					</list>
				</property>
			</bean>
		</property>
	</bean>

	<bean id="domToStringTransformer"
		class="org.springframework.integration.xml.transformer.ResultToStringTransformer" />

	<int:publish-subscribe-channel
		id="businessValidationChannel" />
	<int:publish-subscribe-channel
		id="processorRequestSubscribeChannel" />
	<int:channel id="businessValidationOutputChannel" />
	<int:channel id="sendROChannel" />
	<int:channel id="emfTransformedChannel" />
	<int:channel id="emfChannel" />
	<int:channel id="routeROMessageChannel" />
	<int:channel id="processedKSChannel" />
	<int:channel id="ProcessRoReplayChannel" />
	<int:channel id="ProcessRoChannel" />
	<int:channel id="ProcessRoReplayVerifyChannel" />
	<int:channel id="processROReplayVerifyKSChannel" />
	<int:channel id="emfPayloadlogChannel" />
</beans>

```

****



Certainly! This Spring Integration configuration orchestrates a flow for processing repair orders. Here's a breakdown of the main steps and components involved:

1. **Input Channels:** The process begins with messages coming into various input channels (`businessValidationRequestChannel`, `processorRequestChannel`).

2. **Validation Step:** The initial validations are performed by the `repairOrderBusinessValidationService`. This might involve checking the Vehicle Identification Number (VIN) or other initial checks.

3. **Routing Based on Validation:** There's a commented-out router (`<int:router>`) that was intended to route messages based on the outcome of the initial validation (`true` for success, `false` for failure). However, it's currently inactive.

4. **Message Transformation:** The configuration includes commented-out parts that seem to be aimed at transforming messages and enriching headers (`<int:header-enricher>`, `<int-xml:marshalling-transformer>`), but these are also currently inactive.

5. **Service Activators:** Various `service-activator` components (`repairOrderProcessor`) handle different aspects of the repair order processing, such as checking party receiver IDs, mapping data, logging messages, handling unavailability, and posting messages to Kinesis Stream or JMS (Java Message Service).

6. **Routing Based on Conditions:** Routers (`<int:router>`) are used to direct messages based on certain conditions present in the message headers, like `ro_replay`, `process_kinesis_stream`, etc.

7. **Error Handling:** There are channels (`roErrorHandlingChannel`, `kinesisErrorHandlingChannel`) dedicated to handling errors or scenarios where certain conditions are not met.

8. **Final Output:** Ultimately, the processed messages are directed towards the `responseCreatorChannel`, possibly for further processing or sending responses back to the requester.

This configuration is quite extensive and involves multiple steps and potential paths for message processing, including validations, transformations, conditional routing, error handling, and message output. However, some parts of the flow are currently inactive (commented out).



******

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/integration http://www.springframework.org/schema/integration/spring-integration.xsd
		http://www.springframework.org/schema/integration/file http://www.springframework.org/schema/integration/file/spring-integration-file.xsd
		http://www.springframework.org/schema/integration/xml http://www.springframework.org/schema/integration/xml/spring-integration-xml.xsd
		http://www.springframework.org/schema/oxm http://www.springframework.org/schema/oxm/spring-oxm.xsd
		http://www.springframework.org/schema/integration/ws http://www.springframework.org/schema/integration/ws/spring-integration-ws.xsd
		http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd
		http://www.springframework.org/schema/integration/stream http://www.springframework.org/schema/integration/stream/spring-integration-stream.xsd
		http://www.springframework.org/schema/integration/jdbc http://www.springframework.org/schema/integration/jdbc/spring-integration-jdbc.xsd"
	xmlns:int="http://www.springframework.org/schema/integration"
	xmlns:int-file="http://www.springframework.org/schema/integration/file"
	xmlns:int-xml="http://www.springframework.org/schema/integration/xml"
	xmlns:int-ws="http://www.springframework.org/schema/integration/ws"
	xmlns:oxm="http://www.springframework.org/schema/oxm"
	xmlns:task="http://www.springframework.org/schema/task"
	xmlns:int-jdbc="http://www.springframework.org/schema/integration/jdbc"
	xmlns:int-stream="http://www.springframework.org/schema/integration/stream" >
	
	<!--	Message received after BOD validation has to be passed for business validation -->
	
	<!--	1. Perform VIN checkdigit validation. -->
	<int:service-activator input-channel="businessValidationRequestChannel" ref="repairOrderBusinessValidationService" 
				method="performInitialValidations" output-channel="businessValidationChannel" />
	
	<!--	2. If validation fails, forward the message to response creator channel. else proceed with next validation. -->
    <!-- <int:router input-channel="initialValidatedChannel" expression="headers.messageProcessResultHeader.businessValidationStatus">
             <int:mapping value="true" channel="businessValidationChannel" />
             <int:mapping value="false" channel="responseCreatorChannel" />
    </int:router> -->
    
    <!-- 	3.  Send the transformed message to EIG and update the status in the header "ro_message_send_status"-->
	<!-- <int:header-enricher input-channel="businessValidationChannel" output-channel="businessValidationResponseChannel">
	 	<int:header name="ro_" expression="@eigGateway.exchange(#root).payload" />
	</int:header-enricher> -->
	<int:bridge input-channel="businessValidationChannel" output-channel="businessValidationResponseChannel" />

	<!-- 3 B. Gateway and chain configuration, which marshalls the message, 
		send the message to queue and returns the response -->
	<!-- <int:gateway id="eigGateway" default-request-channel="sendROChannel" 
		/> -->
	<!-- <int:chain input-channel="businessValidationChannel" output-channel="esbChannel"> 
		<int:service-activator ref="repairOrderProcessor" method="mapStarToEmf" /> 
		<int-xml:marshalling-transformer marshaller="roMarshaller" result-transformer="domToStringTransformer" 
		/> </int:chain> -->

	<!-- For successful business validated requests, send the message for EMF 
		processing and Response creation -->
	<int:bridge input-channel="processorRequestChannel" output-channel="processorRequestSubscribeChannel" />
	
	<!-- 3. Service Activator to check whether PartyRecieverId is empty or not and set boolean in ro_replay header. -->
	<int:service-activator id="processROReplayServiceActivator" ref="repairOrderProcessor" method="processROMessage" input-channel="processorRequestSubscribeChannel"
		output-channel="routeROMessageChannel" />

	<!-- 4. If ro_replay true emf message will be post on JMS or send the message to kinesis Stream  -->
	<int:router id="roReplayRouter" input-channel="routeROMessageChannel" expression="headers['ro_replay']">
		<int:mapping value="true" channel="ProcessRoReplayVerifyChannel" />
		<int:mapping value="false" channel="ProcessRoChannel" />
	</int:router>
	
	<!-- 4.A If process_kinesis_stream true message will be post on KS and if false message will be send to MQ -->
	<int:router id="roRouter" input-channel="ProcessRoChannel" expression="headers['process_kinesis_stream']">
		<int:mapping value="true" channel="processKSChannel" />
		<int:mapping value="false" channel="emfChannel" />
	</int:router>
	
	<!-- 5. Service Activator to post message on kinesis Stream. -->
	<int:service-activator id="processKsServiceActivator" ref="repairOrderProcessor" method="processKinesisStream" input-channel="processKSChannel"
		output-channel="emfChannel" />

	<!-- 6. Route the message to emfChannel or send it to error channel  -->
	<!-- <int:router id="roEmfRouter" input-channel="processedKSChannel" expression="headers['ro_message_ks_stram_status']">
		<int:mapping value="success" channel="emfChannel" />
		<int:mapping value="failure" channel="kinesisErrorHandlingChannel" />
	</int:router> -->
	
	<!-- 6.A. Service Activator to convert message from STAR to EMF . -->
	<int:service-activator id="roStartoEmfServiceActivator" ref="repairOrderProcessor" method="mapStarToEmf" input-channel="emfChannel" output-channel="emfEnrichResponseHeaderChannel" />

	<!-- 6.A.1  Service Activator to set Response to SOAP Header elements. -->
	<int:service-activator id="SetSoapResponseHeaderServiceActivator" ref="headerEnricher" method="enrichResponseHeader" input-channel="emfEnrichResponseHeaderChannel" output-channel="emfTransformedChannel" />
	
	<!-- 6.B. Marshallar to convert the emf object to XML . -->
	<int-xml:marshalling-transformer marshaller="roMarshaller" result-transformer="domToStringTransformer" input-channel="emfTransformedChannel" output-channel="emfPayloadlogChannel" />
	
	<!-- 6.B.1 Service Activator to log emf message  . -->
	<int:service-activator id="roEmflogServiceActivator" ref="repairOrderProcessor" method="logEmfPayload" input-channel="emfPayloadlogChannel" output-channel="esbChannel" />
	
	<!-- 7.A If process_ro_replay is true message will send to MQ and if is false will send to error handling channel  . -->
	<int:router id="roReplyVerifyRouter" input-channel="ProcessRoReplayVerifyChannel" expression="headers['process_ro_replay']">
		<int:mapping value="true" channel="ProcessRoReplayChannel" />
		<int:mapping value="false" channel="processROReplayVerifyKSChannel" />
		<int:mapping value="" channel="roErrorHandlingChannel" />
	</int:router>
	
	<!-- 7.B Send ROReplay Message to JMSTemplate . -->
	<int:header-enricher id="roReplayHeaderEnricher" input-channel="ProcessRoReplayChannel" output-channel="processROReplayVerifyKSChannel">
		<int:header name="roReplayCheckFlag" expression="@roReplayGateway.exchange(#root).payload" />
	</int:header-enricher>
    <int:gateway id="roReplayGateway" default-request-channel="roReplayDataChannel" />
    <!-- 7.C Service Activator for StartToEMF & postROReplayMessage. -->
    <int:chain input-channel="roReplayDataChannel">
		<int:service-activator ref="repairOrderProcessor" method="mapStarToEmf" />
		<int-xml:marshalling-transformer marshaller="roMarshaller" result-transformer="domToStringTransformer" />
		<int:service-activator ref="repairOrderProcessor" method="postROReplayMessage" />
   </int:chain>
   
   <!-- 7.D If process_kinesis_stream is true message will send to KS stream and if is false will send to response channel  -->
   <int:router id="roReplyKSRouter" input-channel="processROReplayVerifyKSChannel" expression="headers['process_kinesis_stream']">
		<int:mapping value="true" channel="processROReplayKSChannel" />
		<int:mapping value="false" channel="responseCreatorChannel" />
	</int:router>
	
	<!-- 7.E. Service Activator to post message on kinesis Stream. -->
    <int:service-activator id="processRoKsServiceActivator" ref="repairOrderProcessor" method="processKinesisStream" input-channel="processROReplayKSChannel"
		output-channel="processROReplayEnricherKSChannel" />

	<!-- 7.E.1  Service Activator to set Response to SOAP Header elements. -->
	<int:service-activator id="SetSoapResponseROHeaderServiceActivator" ref="headerEnricher" method="enrichResponseHeader" input-channel="processROReplayEnricherKSChannel" output-channel="responseCreatorChannel" />		

	<!-- 8.A. Service Activator to error Unavailable. -->
	<int:service-activator id="handleUnavailServiceActivator" input-channel="roErrorHandlingChannel" ref="repairOrderProcessor" method="handleRoUnavailability" />

	<!-- 8.B . Send the message response creator channel -->
	<int:bridge input-channel="processorRequestSubscribeChannel" output-channel="responseCreatorChannel" />

	<bean id="messageValidationInterceptor"
		class="com.tms.ddoa.core.interceptor.MessageValidationInterceptor">
		<property name="xsdList">
			<bean
				class="org.springframework.beans.factory.config.ListFactoryBean">
				<property name="targetListClass">
					<value>java.util.ArrayList</value>
				</property>
				<property name="sourceList">
					<list>
						<value>${xsd.path}/envelope.xsd</value>
						<value>${xsd.path}/BODs/Standalone/ProcessRepairOrder.xsd</value>
					</list>
				</property>
			</bean>
		</property>
	</bean>

	<bean id="domToStringTransformer"
		class="org.springframework.integration.xml.transformer.ResultToStringTransformer" />

	<int:publish-subscribe-channel
		id="businessValidationChannel" />
	<int:publish-subscribe-channel
		id="processorRequestSubscribeChannel" />
	<int:channel id="businessValidationOutputChannel" />
	<int:channel id="sendROChannel" />
	<int:channel id="emfTransformedChannel" />
	<int:channel id="emfChannel" />
	<int:channel id="routeROMessageChannel" />
	<int:channel id="processedKSChannel" />
	<int:channel id="ProcessRoReplayChannel" />
	<int:channel id="ProcessRoChannel" />
	<int:channel id="ProcessRoReplayVerifyChannel" />
	<int:channel id="processROReplayVerifyKSChannel" />
	<int:channel id="emfPayloadlogChannel" />
	<int:channel id="emfEnrichResponseHeaderChannel" />
	<int:channel id="processROReplayEnricherKSChannel" />
</beans>
```
***********

[10:46:00] I/launcher - Running 1 instances of WebDriver
[10:46:00] I/hosted - Using the selenium server at http://127.0.0.1:4444/wd/hub
[10:46:00] E/runner - Unable to start a WebDriver session.
[10:46:00] E/launcher - Error: WebDriverError: unknown error: cannot find Chrome binary
  (Driver info: chromedriver=2.46.628402 (536cd7adbad73a3783fdc2cab92ab2ba7ec361e1),platform=Windows NT 10.0.17763 x86_64) (WARNING: The server did not provide any stacktrace information)
Command duration or timeout: 16 milliseconds
Build info: version: '3.8.0', revision: '924c4067df', time: '2017-11-30T11:37:19.049Z'
System info: host: 'EC2AMAZ-KMRG4HQ', ip: '172.70.80.119', os.name: 'Windows Server 2019', os.arch: 'amd64', os.version: '10.0', java.version: '1.8.0_391'
Driver info: driver.version: unknown
    at Object.checkLegacyResponse (D:\BDD-DD365UI\rti-dd365-ui-bdd-vehicles\node_modules\selenium-webdriver\lib\error.js:546:15)
    at parseHttpResponse (D:\BDD-DD365UI\rti-dd365-ui-bdd-vehicles\node_modules\selenium-webdriver\lib\http.js:509:13)
    at D:\BDD-DD365UI\rti-dd365-ui-bdd-vehicles\node_modules\selenium-webdriver\lib\http.js:441:30
    at processTicksAndRejections (node:internal/process/task_queues:95:5)
[10:46:00] E/launcher - Process exited with error code 100


geting this while running `npm run clean-build && protractor typeScript/config/config.js`

**************
[11:29:51] I/http_utils - ignoring SSL certificate
[11:29:51] I/http_utils - ignoring SSL certificate
[11:29:51] I/http_utils - ignoring SSL certificate
C:\Users\ashutosh.majhi\Downloads\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory (2) (1)\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory\node_modules\webdriver-manager\built\lib\binaries\config_source.js:83
                    reject(new Error('response status code is not 200'));
                           ^

Error: response status code is not 200
    at Request.<anonymous> (C:\Users\ashutosh.majhi\Downloads\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory (2) (1)\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory\node_modules\webdriver-manager\built\lib\binaries\config_source.js:83:28)
    at Request.emit (node:events:514:28)
    at Request.onRequestResponse (C:\Users\ashutosh.majhi\Downloads\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory (2) (1)\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory\node_modules\request\request.js:1059:10)
    at ClientRequest.emit (node:events:514:28)
    at HTTPParser.parserOnIncomingClient (node:_http_client:693:27)
    at HTTPParser.parserOnHeadersComplete (node:_http_common:119:17)
    at TLSSocket.socketOnData (node:_http_client:535:22)
    at TLSSocket.emit (node:events:514:28)
    at addChunk (node:internal/streams/readable:376:12)
    at readableAddChunk (node:internal/streams/readable:349:9)

Node.js v20.9.0
****

node:events:492
      throw er; // Unhandled 'error' event
      ^

Error: unable to get local issuer certificate
    at TLSSocket.onConnectSecure (node:_tls_wrap:1659:34)
    at TLSSocket.emit (node:events:514:28)
    at TLSSocket._finishInit (node:_tls_wrap:1070:8)
    at ssl.onhandshakedone (node:_tls_wrap:856:12)
Emitted 'error' event on Request instance at:
    at Request.onRequestError (C:\Users\ashutosh.majhi\Downloads\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory (2) (1)\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory\rti-dd365-ui-bdd-vehicles-feature-dd365new_inventory\node_modules\request\request.js:877:8)
    at ClientRequest.emit (node:events:526:35)
    at TLSSocket.socketErrorListener (node:_http_client:495:9)
    at TLSSocket.emit (node:events:514:28)
    at emitErrorNT (node:internal/streams/destroy:151:8)
    at emitErrorCloseNT (node:internal/streams/destroy:116:3)
    at process.processTicksAndRejections (node:internal/process/task_queues:82:21) {
  code: 'UNABLE_TO_GET_ISSUER_CERT_LOCALLY'
}

Node.js v20.9.0

*****


I/start - java -Dwebdriver.chrome.driver=D:\BDD-DD365UI\rti-dd365-ui-bdd-vehicles\node_modules\webdriver-manager\selenium\chromedriver_2.46.exe -Dwebdriver.gecko.driver=D:\BDD-DD365UI\rti-dd365-ui-bdd-vehicles\node_modules\webdriver-manager\selenium\geckodriver-v0.33.0.exe -jar D:\BDD-DD365UI\rti-dd365-ui-bdd-vehicles\node_modules\webdriver-manager\selenium\selenium-server-standalone-4.0.0-alpha-2.zip.jar -port 4444
[11:04:40] I/start - seleniumProcess.pid: 9856
Error: Invalid or corrupt jarfile D:\BDD-DD365UI\rti-dd365-ui-bdd-vehicles\node_modules\webdriver-manager\selenium\selenium-server-standalone-4.0.0-alpha-2.zip.jar
[11:04:40] I/start - Selenium Standalone has exited with code 1










This Spring Integration configuration outlines a multi-step process for handling messages related to vehicle inventory. Here's a breakdown of the major steps involved:

1. **Business Validations:** Messages undergo initial basic BOD validation before proceeding to business validations.
2. **Perform Business Validations:** Business rules validation is executed using a service activator.
3. **Routing Based on Validation:** Messages are routed based on the outcome of the business validation (true/false).
4. **Routing to Vehicle Details Channels:** Messages are further routed based on specific criteria (e.g., New or Used vehicles).
5. **Transformation and Processing:** Messages go through a series of transformations, including potential transformation from STAR to EMF format and sending messages to AWS Kinesis Stream or other channels.
6. **Header Enrichment:** Headers are enriched with message status information.
7. **Invoke Vehicle Detail Dealer API:** Invocation of external services to fetch vehicle details and dealer configuration data.
8. **Route for Further Processing:** Routing messages based on dealer-specific criteria.
9. **RTI Validations:** Performing Real-Time Integration validations and handling validation outcomes.
10. **Invoke RTI Services:** Invoking RTI services based on validations.
11. **Response Handling:** Handling responses based on service invocation success/failure.
12. **Error Handling:** Managing error scenarios, such as queue unavailability or service invocation failure.
13. **Further Routing:** Routing based on specific vehicle details or destination criteria (e.g., Toyota or Lexus vehicles).
14. **Additional Service Invocations:** Invoking services for used vehicles specific to Toyota or Lexus.

Overall, this configuration sets up a comprehensive flow to validate, process, transform, and handle vehicle inventory-related messages while invoking external services and managing various outcomes at different stages of the process.




*****

Certainly! Here's an explanation of each function in the provided code:

### `refreshOnBoardingForSpecifiedDealersProcessor`
- **Purpose**:
  - Initiates the refreshing process for specified dealer codes.
  - Calls `refreshOnBoardingProcessor` for each dealer code received in the event.

### `refreshOnBoardingProcessor`
- **Purpose**:
  - Handles the complete refreshing process for onboarding information.
  - Retrieves data from various tables (OnBoarding, Gating Table, Dealer Master, DMS Master, Program Master).
  - Calls `refreshOnBoarding` to update onboarding information.
  - Updates OnBoarding table based on configuration settings and handles email alerts.

### Functions for scanning tables:
- `scanOnBoardingTable`, `scanGatingTable`, `scanDealerMasterTable`, `scanDMSMasterTable`, `scanProgramMasterTable`:
  - Purpose: Perform full scans or queries on respective DynamoDB tables based on the provided dealer code or without any filter.
  - Return respective table data as arrays of objects.

### `refreshOnBoarding`
- **Purpose**:
  - Updates OnBoarding table data based on information gathered from various tables.
  - Validates and processes data obtained from the Gating Table against Dealer Master, DMS Master, and Program Master data.
  - Determines updated, failed, and deleted records for the OnBoarding table.

These functions collectively manage the process of refreshing onboarding information for specified dealers or all dealers based on configuration settings. They ensure data integrity, perform validations, and update the OnBoarding table accordingly.



*****


Certainly! Here's an explanation of each function:

### `refreshDealerMasterProcessor`
- **Purpose**:
  - Orchestrates the refreshing process for the `DealerMaster` records.
  - Retrieves records from different sources (DB, DIS, Gating Table), merges, updates, and maintains the `DealerMaster` records.
- **Logic**:
  - Scans the database for existing `DealerMaster` records.
  - Retrieves DIS (Distributed Integration System) dealer records.
  - Retrieves Gating Table records (if required based on configuration).
  - Merges these records and updates the `DealerMaster` table accordingly.

### `scanDealerMaster`
- **Purpose**:
  - Performs a full scan of the `DealerMaster` table.
- **Logic**:
  - Utilizes the `DynamoDbService` to perform a full scan of the `DealerMaster` table.

### `getAllDISDealers`
- **Purpose**:
  - Retrieves all dealer records from DIS based on configured brand IDs.
- **Logic**:
  - Retrieves dealer records from DIS for each configured brand ID.
  - Concatenates these records into a single array.

### `updateDealerMaster`
- **Purpose**:
  - Updates the `DealerMaster` table based on the merged dealer records.
- **Logic**:
  - Handles deletion of specific dealer records.
  - Handles insertion/upsertion of updated dealer records.
  - Sends alert emails for deleted dealers.

### `mergeDISDealerMaster`
- **Purpose**:
  - Merges DIS, current `DealerMaster` records, and Gating Table records to determine updated and deleted dealers.
- **Logic**:
  - Combines DIS records with existing `DealerMaster` records.
  - Identifies dealers to be deleted or updated based on the merged data.
  - Handles the addition of new dealers from the Gating Table.

These functions collectively manage the refresh and synchronization of `DealerMaster` records by fetching data from various sources, updating records accordingly, and maintaining data integrity within the `DealerMaster` table.



*****

Sure, here's an explanation of each function:

### `construtGatingRule`
- **Purpose**:
  - Constructs or updates the gating rules for a given dealer association based on associated programs.
- **Parameters**:
  - `request`: An instance of `DealerAssociation` representing dealer association details.
  - `currentGatingRule`: An instance of `GatingTableResponse` representing the current gating rules.
- **Logic**:
  - Iterates over the associated programs of the given dealer.
  - Builds or updates the rules for the specific DSP (Dealer Service Provider) associated with each program.
  - Handles various scenarios like creating new DSP rules if not existing, updating existing rules, and merging authorization and business rules.

### `prepareHtmlTableWithData`
- **Purpose**:
  - Prepares an HTML table with the given JSON data.
- **Parameter**:
  - `emailJSONData`: An array of JSON objects to be represented in the table.
- **Logic**:
  - Generates HTML table markup with rows and columns based on the provided JSON data.

### `isArrayOfObject1PresentInObject2`
- **Purpose**:
  - Checks if an array of objects (`obj1`) is completely present in another array of objects (`obj2`).
- **Parameters**:
  - `obj1`: Array of objects to check presence.
  - `obj2`: Array of objects to check within.
- **Logic**:
  - Compares each object in `obj1` to all objects in `obj2` and checks if all objects in `obj1` are present in `obj2`.

### `isObjectsEqual`
- **Purpose**:
  - Compares two objects for equality.
- **Parameters**:
  - `obj1`: First object to compare.
  - `obj2`: Second object to compare.
- **Logic**:
  - Recursively compares each property in `obj1` with the corresponding property in `obj2`.
  - Handles different types of properties (strings, objects, arrays) and checks for equality.

These functions primarily deal with constructing/updating gating rules, preparing HTML tables, and performing object comparisons for equality or presence in other arrays/objects.



***



This code comprises TypeScript functions for refreshing gating rules based on different conditions.

### `refreshGatingRulesForRequest`
- **Function Signature**: 
  - This function is `async` and takes a single parameter `requestBody` of type `RequestGatingRequest`.
- **Function Logic**:
  - It initializes a `serviceResponse` array to store the results.
  - Processes each dealer asynchronously from the `requestBody.dealers` array:
    - Validates the incoming dealer association details using `validate`.
    - If the request is invalid, it logs the details and pushes an error response object to `serviceResponse`.
    - Otherwise, it creates a new `GatingTableResponse` object, populates it with the dealer details, and generates gating rules based on associated programs.
    - If no programs are associated with the dealer, it marks the request as invalid and adds an error message to `serviceResponse`.
  - Logs the `serviceResponse` after processing the requests.
  - Determines the HTTP status code based on the presence of error messages in the `serviceResponse`.
  - Updates `constants.CALL_BACK_RESPONSE` object with the determined status code and constructs a JSON response based on certain conditions:
    - If `canUpdateTable` is `true` and the status code is 200, it updates the DynamoDB table, constructs a response message, prepares HTML content with the updated data, and sends an alert mail.
    - Otherwise, it constructs a response body containing `serviceResponse`.

### `refreshAllGatingRulesForOnBoarding`
- **Function Signature**: 
  - This function is `async` and takes a single parameter `requestBody` of type `any`.
- **Function Logic**:
  - Performs a full scan on the `onBoardingTable` in DynamoDB to fetch all `OnBoarding` items.
  - Extracts `dealerCd` from each item and stores them in the `dealerCds` array.
  - Invokes `refreshGatingRulesForRequest` by passing an object containing `canUpdateTable` (or `false` if not provided) and the array of `dealerCds`.

These functions primarily process dealer association details, validate them, generate gating rules based on associated programs, and perform updates on the DynamoDB table if certain conditions are met. Additionally, it sends alert mails with relevant data when updating the table.


****

This code represents a TypeScript function called `generateGatingRulesForRequest` responsible for processing an array of `DealerAssociation` objects to generate gating rules based on certain conditions. Here's a breakdown:

- **Imports**: This section imports various modules, services, models, validators, and utility functions required for this file's operation.

- **Function Signature**: 
  - The function `generateGatingRulesForRequest` is asynchronous (`async`) and expects two parameters:
    - `request`: An array of `DealerAssociation` objects.
    - `overwritePrograms`: A boolean flag indicating whether to overwrite existing programs or not.

- **Function Logic**:
  - It initializes a `serviceResponse` array to store the results.
  - Validates the incoming `request` using the `validate` function from the gating-master-validator module.
  - Processes each item in the `request` array asynchronously using `Promise.all`:
    - If the request is invalid, it logs the details and pushes an error response object to `serviceResponse`.
    - Otherwise, it retrieves current gating rules for the dealer (`req.dealerCd`) and updates the gating rules based on the associated programs.
    - If there are warning messages, it creates a warning response object; otherwise, it pushes the updated gating rule object to `serviceResponse`.
  - Logs the `serviceResponse` after processing the requests.
  - Determines the HTTP status code based on the presence of error messages in the `serviceResponse`.
  - Constructs a response object with the determined status code, content type, and the `serviceResponse` as JSON in the body.

The final part of the code, currently commented out, seems to involve updating a DynamoDB table (`AppConfig.dspDealerMappingTable`) if certain conditions are met and returning a success or failure response accordingly.

This function mainly processes incoming `DealerAssociation` objects, generates gating rules based on their details, and constructs a response indicating the success or failure of the process.

*****


#Gating rules lamda

Certainly! The `serverless.yml` file defines several Lambda functions and their configurations:

1. **generateGatingRules:** This Lambda handles the generation of gating rules. It's triggered by an HTTP POST request to `/generateGatingRules/v1/`.

2. **refreshGatingRules:** This Lambda refreshes gating rules. It's triggered by an HTTP POST request to `/refreshGatingRules/v1`.

3. **refreshAllGatingRules:** This Lambda refreshes all gating rules from the OnBoarding table. It's triggered by an HTTP POST request to `/refreshAllGatingRules/v1`.

4. **refreshDealerMaster:** This Lambda refreshes the Dealer Master table from DIS (Dealer Information System). It runs on a schedule defined by a cron expression.

5. **refreshOnBoardingForAllDealers:** This Lambda refreshes the OnBoarding table for all dealers. It runs on a schedule defined by a cron expression.

6. **refreshOnBoardingForSpecificDealer:** This Lambda refreshes the OnBoarding table for specific dealers.

7. **getDealersForProgram:** This Lambda retrieves dealer codes for a given program code. It's triggered by an HTTP GET request to `/getDealersForProgram/v1/{programCd}`.

8. **getDealersForDsp:** This Lambda retrieves dealer code and enrolled program details for a given DSP (Dealer Service Provider) code. It's triggered by an HTTP GET request to `/getDealersForDsp/v1/{dspCd}`.

9. **getDealerDetails:** This Lambda retrieves details for a specific onboarding dealer. It's triggered by an HTTP GET request to `/getDealerDetails/v1/{dealerCd}`.

Each function has its specific purpose and trigger events defined, such as HTTP requests or scheduled executions, and they often have associated authorization configurations for handling access control.

****







*****





Certainly! This code seems to be an extensive module responsible for updating a database based on the contents of files stored in an AWS S3 bucket. Let's break it down:

1. **Imports:** The code imports necessary modules from various libraries (`aws-sdk`, `pg`, `lodash`) and internal configurations (`AppConfig`, `constants`) required for AWS services, database operations, and other utilities.

2. **Functions:**
    - `databaseUpdate`: This function takes an array of files and a pool configuration object as input and performs multiple operations:
        - Reads an indicator file from S3 to get MD5 details.
        - Checks the MD5 integrity against the files in the array.
        - Processes the files, creates SQL queries, and inserts records into the database based on certain conditions.
        - Deletes files from S3 based on specific conditions.
        - Returns a boolean indicating the success or failure of the database update.

    - Other functions within `databaseUpdate` are utility functions for specific database operations (`insertHDRTable`, `insertLineItemTable`, etc.), creating notifications for certain file types, and checking MD5 integrity (`checkmd5` and `readIndicatorFile`).

3. **Asynchronous Operations:**
    - The code extensively uses `async/await` with `Promise` objects to handle asynchronous tasks, especially while interacting with AWS S3, the PostgreSQL database, and performing other file-related operations.

4. **Error Handling:**
    - The code includes error handling using `try/catch` blocks and logs detailed error messages using the `log` utility from `@tmna-libraries/node-commons`.

5. **Database Operations:**
    - Several functions (`insertHDRTable`, `insertLineItemTable`, etc.) execute SQL queries on the PostgreSQL database using the `pg` library's `Pool` object.

6. **S3 Operations:**
    - Functions like `readIndicatorFile` and `deleteSource` interact with AWS S3 using the `AWS.S3` object to read indicator files and delete files from the S3 bucket.

7. **Business Logic:**
    - The code contains specific business logic to handle different file types, create notifications for different entities based on database records, and manage the execution flow based on certain conditions.

Overall, this module orchestrates file processing from an S3 bucket, MD5 validation, database updates, and associated notifications and error handling. It's quite comprehensive, covering various aspects of file management and database interactions within an AWS environment.





***


This code appears to be an AWS Lambda function written in TypeScript that serves to perform various operations based on an event trigger, likely associated with AWS S3 bucket actions. Here's a breakdown of the main parts:

1. **Imports:** The code imports various AWS SDK modules (`aws-sdk`) and other custom-defined modules for configuration, logging, database operations, etc.

2. **Functions:**
   - `notifyLaborOperations`: This is the main Lambda handler function. It's triggered by an event (presumably an S3 event) and performs several tasks:
     - Initializes configuration and credentials.
     - Lists objects in an S3 bucket's 'outgoing/' folder.
     - Updates a database based on the listed contents.
     - Deletes a specific file from the S3 bucket.
     - Sends alert emails based on the success or failure of the operations.
     - Stops an EC2 instance.
     - Returns appropriate responses based on success or failure.

   - `deleteIndicator`: A function responsible for deleting a specific object from an S3 bucket.

3. **Variable Declarations:**
   - `poolConfig`: Configuration object for a PostgreSQL connection.
   - `deleteparams`: Parameters object for deleting an object from an S3 bucket.
   - `s3`: Instance of the AWS S3 service.

4. **Asynchronous Operations:**
   - The code uses asynchronous operations (`async/await`) extensively, especially when interacting with AWS services (S3, EC2), handling database operations, and sending emails.
   - It uses `Promise` objects to handle asynchronous behavior and employs `try/catch` blocks to catch and handle errors.

5. **Error Handling and Notifications:**
   - The code handles errors by logging them and sending alert emails, then stopping the EC2 instance. It returns appropriate HTTP status codes (`200` for success, `500` for errors) along with descriptive messages.

6. **External Dependencies:**
   - It relies on external configurations, credentials, and constants imported from other files (`AppConfig`, `constants`, etc.).

Overall, this Lambda function seems to manage a workflow that involves processing files in an S3 bucket, updating a PostgreSQL database, and performing other associated tasks with error handling and notifications for success or failure.



*****

Certainly! This code snippet appears to be a function responsible for stopping an EC2 instance using the AWS SDK.

Here's a breakdown:

1. **Imports:** The code imports necessary modules like `AWS` from the AWS SDK, `log` from the '@tmna-libraries/node-commons', `AppConfig` from a configuration file, and `constants` from a utility file.

2. **`stopEc2Instance` function:**
    - This function is designed to stop an EC2 instance.
    - It begins by creating an instance of the `AWS.EC2` service.
    - It prepares the parameters required for the `stopInstances` method, specifying the instance ID(s) to stop (retrieved from `AppConfig.bastionId`), and sets `DryRun` to `false`.
    - Updates the AWS configuration with the specified region from `AppConfig`.
    - Calls the `stopInstances` method asynchronously, which takes the prepared parameters and a callback function.
    - Inside the callback function, it checks for errors or successful data returned from the `stopInstances` operation.
    - If an error occurs during the `stopInstances` call, it logs an error message using the `log.error` method.
    - If the operation is successful, it logs a success message with details about the stopped instances.

3. **Error Handling:**
    - The code employs a try/catch block to catch any synchronous errors that might occur during the setup or EC2 stopping process.
    - If any error is caught, it's logged using `log.error` and rethrown as an error for potential higher-level error handling.

This function encapsulates the logic to stop an EC2 instance using the AWS SDK, handles errors that might occur during the process, and logs the status of the operation accordingly.

***"
This function, `deleteSource`, is responsible for deleting a file from an S3 bucket using the AWS SDK's `deleteObject` method. Here's a breakdown:

1. **Imports:** The code imports `log` from `@tmna-libraries/node-commons` for logging purposes and `constants` from a utility file containing constant values.

2. **`deleteSource` function:**
    - Takes three parameters: `s3` (the AWS S3 service object), `bucketName` (name of the S3 bucket), and `fileName` (name of the file to delete).
    - Returns a `Promise<any>` to handle the asynchronous deletion operation.

3. **Deleting the Object:**
    - Prepares the `deleteparams` object with the required parameters - the `Bucket` name and the `Key` (the path to the file in the S3 bucket).
    - Uses `s3.deleteObject` to delete the specified file asynchronously.
    - Provides a callback function that handles the result of the deletion operation.
    - If an error occurs during deletion, it logs an error message using `log.error`, mentioning the file name and the error details, then resolves the Promise with `false` to indicate a failure.
    - If the deletion is successful, it logs a success message using `log.info` and resolves the Promise with `true` to indicate success.

4. **Promise Handling:**
    - Wraps the deletion operation inside a `Promise` to manage the asynchronous nature of the `deleteObject` function.
    - `await` is used to ensure the completion of the deletion operation before resolving or rejecting the `Promise`.

This function encapsulates the logic to delete a specific file (`fileName`) from a specified S3 bucket (`bucketName`) using the AWS SDK's `deleteObject` method, providing error logging and Promise-based handling for deletion success or failure.


****

This function, `sendAlertMail`, is responsible for sending alert emails using AWS SES (Simple Email Service). Here's a breakdown:

1. **Imports:** The code imports necessary modules like `AWS` from the AWS SDK, `log`, and `tmnaInit` from `@tmna-libraries/node-commons`, `Callback`, `Context` from 'aws-lambda', `AppConfig` from a configuration file, and `constants` from a utility file.

2. **`sendAlertMail` function:**
    - Takes three parameters: `status` (boolean indicating success or failure), `errorObj` (details about the error), and `context` (AWS Lambda context).
    - Uses `tmnaInit` to initialize the configuration using `AppConfig` and the AWS request ID from the Lambda context.
    - Creates an instance of the AWS SES service (`ses`).
    - Prepares `params` object required for the SES `sendEmail` method:
        - Defines the email recipient's address from `AppConfig.recipientMailAddress`.
        - Defines the sender's address from `AppConfig.senderMailAddress`.
        - Sets the email subject and body based on the `status` and `errorObj` provided.
    - Uses `ses.sendEmail` to send the email asynchronously.
    - Provides a callback function that handles the result of the email sending operation:
        - If there's an error during the email sending process, it logs an error message using `log.error`.
        - If the email is sent successfully, it logs a success message using `log.info`.

3. **SES Configuration:**
    - The function assumes the AWS credentials and configuration are already set up and available through the AWS SDK.

This function encapsulates the logic to send alert emails using AWS SES based on the provided status (success/failure) and error details. It uses predefined constants for email subjects and messages, and it logs the success or failure of the email sending process for monitoring and error tracking purposes.


