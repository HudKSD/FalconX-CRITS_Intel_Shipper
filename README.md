===========================================
 User Manual
===========================================

###CrowdStrike-CRITs Connection Application
###
### CRITS (Collaborative Research into Threats )
CRITs is a web-based tool which combines an analytic engine with a cyber threat database that not 
only serves as a repository for attack data and malware, but also provides analysts with a powerful 
platform for conducting malware analyses, correlating malware, and for targeting data. These analyses 
and correlations can also be saved and exploited within CRITs. CRITs employs a simple but very useful 
hierarchy to structure cyber threat information. This structure gives analysts the power to 'pivot' on 
metadata to discover previously unknown related content

### Crowd Strike (Falcon) Threat Intelligence
Cyber threat intelligence is what cyber threat information becomes once it has been collected, 
evaluated in the context of its source and reliability, and analyzed through rigorous and structured 
tradecraft techniques by those with substantive expertise and access to all-source information. Like 
all intelligence, cyber threat intelligence provides a value-add to cyber threat information, which 
reduces uncertainty for the consumer, while aiding the consumer in identifying threats and opportunities. 
It requires that analysts identify similarities and differences in vast quantities of information and detect 
deceptions to produce accurate, timely, and relevant intelligence.

### Integration (CrowdStrike-CRITs connector & Updater)
The Crowd strike Falcon API will serve as the main source of cyber threat intelligence for CRITs. 
The integration will involve a connecting application, that would be capable of updating the CRITS 
database periodically, with specific parameters which can be assigned or tuned by the user (analyst).
The application will also understand the intelligence formatting of Crowd Strike and translate it to 
a format usable by the CRITS platform.

The application would leverage several connections and would be built to scale in such a way that, 
its functionality can be extended for future purposes. Hence, the sole purpose of the application is to 
supply intelligence to CRITs and update the former when newly added threats are appended to Crowd Strike.
The diagram below details the architecture of the connecting application, that would be responsible for 
crowd strike and CRITS operability:

The figure above is made up of:
	1.	Crowd strike intelligence source (cloud) Note: This is will only be used by the 
		Crowdstrike API and has nothing to do with the security operations center 
		(this was included for illustration purposes).
	2.	Crowd strike API
	3.	The Application
	4.	CRITS

The Application in the above illustration includes:
	o	Data intake
	o	Crowdstrike CRITS vocabulary
	o	Mapper
	o	Data output



===========================================



### GETTING STATED
###
This program will be used to update the CRITs Mongo (Hudgo) DB for newly
added CrowdStrike (indicator) intelligence
NOTE: Internet connectivity is required

### DIRECTORY STRUCTURE
###
.
		├── Documentation
		│   ├── Application\ (CrowdStrike-CRITS\ \ Intel\ Connector\ &\ Updator).jpg
		│   └── Intel\ APIs\ _\ Documentation\ _\ Support\ _\ Falcon.pdf
		├── _end.sh
		├── Engine
		│   ├── functions_for_vocabulary.py
		│   ├── hudgo.py
		│   ├── intel_base.py
		│   ├── logger.py
		│   ├── Paths.py
		│   ├── process_config_data.py
		│   ├── push_intelligence.py
		│   ├── report_generator.py
		│   ├── requirements.txt
		│   ├── tune.py
		│   ├── utils.py
		│   └── vocabulary.py
		├── LICENSE
		├── README
		├── Reports
		│   ├── guide.txt.txt
		│   ├── program_log
		│   └── report.report
		├── _run.sh
		├── settings.ini
		└── _status.sh

		3 directories, 23 files


### CODE STRUCTURE
###
helpers   =>  logger | settings | hudgo | process_config_data
corefiles =>  vocabulary.py --> vocab_input.py --> tune.py --> push_intelligence.py


### DEPENDENCIES
###
-before anything, go into the Engine/ directory and run this command
-pip install -r requirements.txt


### CONFIGURATION
###
To commence the application use one must configure the program accordingly:
navigate to the root directory of the program and open *setting.ini*
The file should look like this:

		[crowdstrike_API]
		date = 0                                     	X
		api_key =                               		X
		api_url = https://intelapi.crowdstrike.com		X
		api_id  =                       				X
		perpage = 100									O
		
		[crits]
		crits_user = admin								O
		crits_sourcename = crowdstride					O

		[hudgoDB]
		host = 											O
		port = 27017									O
		database = crits								X
		indicators_collection = indicators				X
		actors_collection = actors						X
		domains_collection = domains					X

		[timer]
		timer = 1										O


[crowdstrike_API] 	= 	contains settings for Falcon API
[hudgoDB]     		= 	contains settings for CRITs database
[timer]				= 	the timer is the interval at which the CRITS Db
						is populated
						
NOTE: 	once configured all areas marked X should remain untouched
		which all areas marked O can undergo changes.

IMPORTANT:
MAKE SURE TO CREATE A SOURCE IN CRITS AND FILL IN THE ****[crits]****
SECTION TOGETHER WITH THE USER NAME

### RUN
###
To execute the application (Linux)

	sh _run.sh or ./_run.sh or = to run the app
	sh _status.sh or ./_status = to check the status of the app
	sh _end.sh or ./_end.sh    = to end the program
	
	Hence, you need to be at the 
	root directory to use these
	functions.
	
	NOTE: 	After the last action, one can check the status once again
			to confirm the shutdown

For developers (advanced users): 	Python push_intelligence.py
									hence, to kill the process *kill -9 PiD*
									this should be used when debugging


### REPORTING AND LOGGING
###
Please see, Reports directory and review *guide.txt*


===========================================



### APPLICATION ARCHITECHTURE
###
### With reference to the image diagram in the Documentation directory:
Data intake			This will act as the main source of data input into the application, 
				    (Ingestor/ connector to Crowdstrike) from which, data will be provided to CRITs.
Crowdstrike CRITS   vocabulary	The vocabulary serves as a dictionary, to enhance  communication between Crowdstrike and CRITS.
				    This serves as the main source of understanding.
Mapper				The mapper is an actionable operation that utilizes the vocabulary and hence, 
				    enacts the communication flow between the two entities.
Data output			The data out will be the sole means of getting data into CRITS.



===========================================


### PROCESS TO ADD MORE TLOS TO THE APPLICATION SOURCE CODE
###
1.Add a collection to settings.ini
2.Add the linking parser to the parameter tab in push_intelligence.py
3.Add the respective object schema from tune into the object schema section in push_intelligence.py
4.Add and operation into the operations tab in push_intelligence.py
5.In operation, copy the sample code and replace, the col* and doc* in the insert & query functions respectively
6.NOTE: make sure to modify the schema version accordingly
7.Now we go to tune and add the object relationship to the indicator schema
8.Make sure you have a schema for the object in tune.py as a function **tune_##()
9.Create a function to functions_of_vocabulary in case you need to cleanse data for the schema
