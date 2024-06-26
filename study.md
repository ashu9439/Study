``` package main

import (
	"fmt"
	"math"
)

// shape interface
type shape interface {
	area() float64
	circumf() float64
}

type square struct {
	length float64
}
type circle struct {
	radius float64
}

// square methods
func (s square) area() float64 {
	return s.length * s.length
}
func (s square) circumf() float64 {
	return s.length * 4
}

// circle methods
func (c circle) area() float64 {
	return math.Pi * c.radius * c.radius
}
func (c circle) circumf() float64 {
	return 2 * math.Pi * c.radius
}

func printShapeInfo(s shape) {
	fmt.Printf("area of %T is: %0.2f \n", s, s.area())
	fmt.Printf("circumference of %T is: %0.2f \n", s, s.circumf())
}

func main() {
	shapes := []shape{
		square{length: 15.2},
		circle{radius: 7.5},
		circle{radius: 12.3},
		square{length: 4.9},
	}

	for _, v := range shapes {
		printShapeInfo(v)
		fmt.Println("---")
	}
}```







package main

import (
	"fmt"
	"time"
)

func main() {
	startTime := time.Now()
	defer func() {
		fmt.Println("total time taken =  ", time.Since(startTime))
	}()

	/*
		isCompleted := make(chan bool)
		go parralellProcess(isCompleted)

		//* <-isCompleted *: Blocks the main goroutine until it receives a value from the "isCompleted channel"
		fmt.Println("parallel process completed ? - ", <-isCompleted)
	*/

	/*
	   // below code will cause error , since default capacity for a chanell = 0 , i.e. if someting put into the cahnnel by any process then it must be immediately consume by the parent/ main process
	   // but here it is not immediately consuming, instead it is waiting till the executing go till the next line
	   	ch := make(chan string)
	   	ch <- "1st msg"
	   	fmt.Println("data in channel ch - ", <-ch)
	*/

	ch := make(chan string, 2)
	// channels is like a FIFO queue
	ch <- "1st msg"
	ch <- "2nd msg"
	fmt.Println("data in channel ch - ", <-ch)
	fmt.Println("data in channel ch - ", <-ch)
	
	
	
	
	
	
	
	
	
	
	
	
	
	

}

func parralellProcess(isCompleted chan bool) {
	time.Sleep(time.Second * 2)
	isCompleted <- true
}




























Absolutely! Updating the algorithm based on the information provided:

### Revised Algorithm:

1. **Check Root Access**  
   - Check if the script is running as root. If not, execute as root using sudo.

2. **File Existence Check**
   - Verify the existence of 'opcodeindicator.dat' file in the S3 bucket 's3://ddoa-qa-dr-opcode/incoming/'.

3. **Perform File Operations**
   - Delete specific files with defined patterns from the local working directory.

4. **Define AWS S3 Bucket Paths**
   - Set variables to store paths to various directories within the S3 bucket:
     - `INCOMING_FILES`, `OUTGOING_FILES`, `OUTPUT_FILES`, etc.

5. **Trigger an AWS Glue Job (`ddoa-qa-dr-opcode.py`)**
   - Initiate the Glue job to process CSV files from the 'ddoa-qa-dr-opcode/incoming' directory.

6. **Monitor Glue Job Execution**
   - Continuously check the status of the Glue job:
     - Await completion of the Glue job or monitor for any failure.

7. **Based on Glue Job Status**
   - If the Glue job fails:
     - Move specific files from the incoming directory to a 'Backlog' folder within the S3 bucket.
   - If the job is successful:
     - Archive these files in an 'Archive' folder within the S3 bucket.

8. **File Processing (Glue Job Output)**
   - The Glue job transforms CSV files.
   - The transformed data is added to specific S3 paths for different details and full categories for Toyota and Lexus:
     - `STAGE_DETAIL_TOY='s3://ddoa-qa-dr-opcode/stage/details/toy/'`
     - `STAGE_DETAIL_LEX='s3://ddoa-qa-dr-opcode/stage/details/lex/'`
     - `STAGE_FULL_TOY='s3://ddoa-qa-dr-opcode/stage/full/toy/'`
     - `STAGE_FULL_LEX='s3://ddoa-qa-dr-opcode/stage/full/lex/'`

9. **File Transfer (Possibly SFTP)**
   - (Commented section): Transfer files to a remote server using 'sshpass' and 'sftp'.

10. **Notification**
    - Generate an 'opcode-files-ready.dat' file containing MD5 checksums of processed files.
    - Upload the 'opcode-files-ready.dat' file to a specific location within the S3 bucket.

11. **Conclusion**
    - Perform cleanup operations (remove files).
    - Exit the script.

### Glue Job Role:

- The Glue job (`ddoa-qa-dr-opcode.py`) processes CSV files from the 'ddoa-qa-dr-opcode/incoming' directory.
- It performs transformations on these CSV files and updates corresponding locations within the S3 bucket for detailed and full data categories related to Toyota and Lexus (`STAGE_DETAIL_TOY`, `STAGE_DETAIL_LEX`, `STAGE_FULL_TOY`, `STAGE_FULL_LEX`).


*****
Certainly! Here's a comprehensive algorithmic breakdown of the provided shell script:

```
1. Check if the script is running as root. If not, execute as root using sudo.

2. Verify the existence of 'opcodeindicator.dat' file in the S3 bucket 's3://ddoa-qa-dr-opcode/incoming/'.

3. Perform file operations:
   - Delete specific files with certain patterns from the local working directory.

4. Define AWS S3 bucket paths:
   - INCOMING_FILES, OUTGOING_FILES, OUTPUT_FILES, etc.

5. Trigger an AWS Glue job named 'ddoa-qa-dr-opcode.py'.

6. Continuously monitor the status of the Glue job:
   - Check if it's running or has failed.

7. Based on Glue job status:
   - If the job fails:
     - Move specific files from the incoming directory to a 'Backlog' folder within the S3 bucket.
   - If the job is successful:
     - Archive these files in an 'Archive' folder within the S3 bucket.

8. File Processing:
   - Copy files from specific S3 locations to the local EC2 instance.
   - Use Java with 'saxon9he.jar' to perform XSLT transformations on XML files.
   - Remove unnecessary files after transformations.
   - Compress XML files using 'gzip'.
   - Copy compressed files back to specified locations within the S3 bucket.

9. File Transfer (Possibly SFTP):
   - (Commented section): Transfer files to a remote server using 'sshpass' and 'sftp'.

10. Notification:
    - Create an 'opcode-files-ready.dat' file containing MD5 checksums of processed files.
    - Upload the 'opcode-files-ready.dat' file to a specific location within the S3 bucket.

11. Conclusion:
    - Perform cleanup operations (remove files).
    - Exit the script.

```

This algorithm outlines the step-by-step actions performed by the shell script, including checks, file operations, AWS Glue job execution, file processing (transformation, compression), potential file transfers, and final notifications/cleanup.
****
****
This script appears to be a shell script designed to perform a series of operations on files stored in an AWS S3 bucket. Let's break down its functionality:

1. **Check Root Access:**
   - Checks if the script is running as root. If not, it attempts to execute the script as root using `sudo`.

2. **File Existence Check:**
   - Verifies the existence of a particular file (`opcodeindicator.dat`) in an S3 bucket (`s3://ddoa-qa-dr-opcode/incoming/`).

3. **File Operations:**
   - Deletes specific files based on patterns.
   - Defines various S3 bucket paths for incoming, outgoing, output, stage, backlog, and archive files.

4. **Glue Job Execution:**
   - Uses AWS CLI (`aws glue start-job-run`) to trigger an AWS Glue job named `ddoa-qa-dr-opcode.py`.

5. **Monitoring Glue Job Execution:**
   - Monitors the status of the Glue job to check if it's still running or has failed.

6. **File Movement and Handling based on Glue Job Status:**
   - Moves files to the backlog folder in case the Glue job fails.
   - Archives files if the Glue job runs successfully.

7. **File Processing:**
   - Copies files from S3 to the local EC2 directory.
   - Executes XSLT transformations using Java (`saxon9he.jar`) to generate XML files.
   - Compresses XML files using gzip.
   - Copies the compressed files back to specific S3 locations (`OUTGOING_FILES` and `OUTPUT_FILES`).

8. **File Transfer (Possibly SFTP):**
   - Potentially transfers files using `sshpass` and `sftp` to a remote server.

9. **Notification:**
   - Generates an `opcode-files-ready.dat` file containing MD5 checksums of processed files and uploads it to an S3 bucket (`OUTGOING_FILES`).

10. **Script Conclusion:**
    - The script concludes with cleanup (`rm` commands) and an exit statement.

Please note:
- The script is interacting heavily with AWS services using the AWS CLI.
- It performs various file operations, S3 bucket interactions, and potentially transfers files to a remote server.
- It uses Java and XSLT for XML transformations.
- It checks the status of an AWS Glue job and handles file movement accordingly based on the job's status.


****

Absolutely, let's dive deeper into the script's various sections and understand what each part accomplishes:

### Root Check
The script checks if it's running as the root user (`uid=0`). If not, it attempts to execute the script with root privileges using `sudo`.

### File Existence Check
It verifies the existence of a specific file (`opcodeindicator.dat`) located in the AWS S3 bucket (`s3://ddoa-qa-dr-opcode/incoming/`). This check determines whether further operations in the script will proceed based on the existence of this file.

### File Operations
Several operations involve deleting specific types of files:
- Deleting files with certain patterns (`run-*`, `OA-TOY-OC*.xml`, etc.) from the local working directory.

### AWS S3 Bucket Paths
The script defines several variables storing paths to different directories within the S3 bucket: `INCOMING_FILES`, `OUTGOING_FILES`, `OUTPUT_FILES`, etc. These paths are used throughout the script to reference specific locations in the S3 bucket.

### Glue Job Execution
The script triggers an AWS Glue job named `ddoa-qa-dr-opcode.py` using the AWS CLI command `aws glue start-job-run`.

### Monitoring Glue Job Execution
The script continuously checks the status of the Glue job to determine if it is running, failed, or completed. It uses the AWS CLI (`aws glue get-job-runs`) and loops with a `sleep` command to wait for the Glue job to complete.

### File Movement and Handling based on Glue Job Status
- If the Glue job fails, the script moves certain files from the incoming directory to a `Backlog` folder within the S3 bucket.
- If the Glue job is successful, the script archives these files in an `Archive` folder within the S3 bucket.

### File Processing
- Copies files from specific locations within the S3 bucket to the local EC2 instance.
- Uses Java with `saxon9he.jar` to perform XSLT transformations on XML files.
- Removes unnecessary files after transformations.
- Compresses XML files using `gzip`.
- Copies the compressed files back to specified locations within the S3 bucket.

### File Transfer (Possibly SFTP)
There's a commented-out section that suggests transferring files to a remote server using `sshpass` and `sftp`. This part might be used to transfer files to another server for further processing or storage.

### Notification
Creates an `opcode-files-ready.dat` file containing MD5 checksums of processed files. This file is uploaded to a specific location within the S3 bucket.

### Conclusion
The script concludes with some cleanup operations (`rm` commands) and exits. 

This script is designed for handling a series of file operations, interacting heavily with AWS services (S3, Glue), performing transformations on XML files, and potentially transferring files to a remote server. It's orchestrated to manage data flow and processing within an AWS environment, specifically handling files for a particular data pipeline or workflow.





















```
#!/bin/sh

#
# Am I root?
#
uid=$(id -u)
if [ x$uid != x0 ]
then
    printf -v cmd_str '%q ' "$0" "$@"
    exec sudo su -c "$cmd_str"
    # I am root!
fi
#
#    Execute the following command ahead of time to
#    activate the crontab in Linux.
#    Once this is in place, everytime when an INDICATOR_FILE
#    file is present, this shell script is executed.
#
#    crontab -e
#    */5 * * * * /home/ec2-user/opcode/transform.sh
#

#
# Is there something to process?
#
INDICATOR_FILE='s3://ddoa-qa-dr-opcode/incoming/opcodeindicator.dat'
WORKING_DIRECTORY='/home/ec2-user/opcode/'
cd $WORKING_DIRECTORY

exists=$(aws s3 ls $INDICATOR_FILE)
if [ -z "$exists" ]; then
  echo "File" $INDICATOR_FILE
  echo "does not exist"
else
  #
  # We delete previous run files
  #
  rm -f run-*
  rm -f OA-TOY-OC*.xml
  rm -f OA-LEX-OC*.xml
  rm -f OA-TOY-OC*.xml.gz
  rm -f OA-LEX-OC*.xml.gz
  rm -f input-delta-toyota.xml
  rm -f input-full-toyota.xml
  rm -f input-delta-lexus.xml
  rm -f input-full-lexus.xml
  TIMEDATE=$(TZ=America/Chicago date +'%Y%m%d_%H%M%S')
  #
  # Define file locations
  #
  INCOMING_FILES='s3://ddoa-qa-dr-opcode/incoming/'
  OUTGOING_FILES='s3://ddoa-qa-dr-opcode/outgoing/'
  OUTPUT_FILES='s3://ddoa-qa-dr-opcode/output/'
  STAGE_DETAIL_TOY='s3://ddoa-qa-dr-opcode/stage/details/toy/'
  STAGE_DETAIL_LEX='s3://ddoa-qa-dr-opcode/stage/details/lex/'
  STAGE_FULL_TOY='s3://ddoa-qa-dr-opcode/stage/full/toy/'
  STAGE_FULL_LEX='s3://ddoa-qa-dr-opcode/stage/full/lex/'
  BACKLOG_FILES='s3://ddoa-qa-dr-opcode/backlog/'
  ARCHIVE_FILES='s3://ddoa-qa-dr-opcode/archive/'
  #
  # Glue process begins
  #
  aws s3 rm $STAGE_DETAIL_TOY --recursive --include "run-*"
  aws s3 rm $STAGE_DETAIL_LEX --recursive --include "run-*"
  aws s3 rm $STAGE_FULL_TOY --recursive --include "run-*"
  aws s3 rm $STAGE_FULL_LEX --recursive --include "run-*"
  echo "Run Glue job"
  aws glue start-job-run --job-name ddoa-qa-dr-opcode.py --region us-east-1
  echo "Delete indicator file"
  aws s3 rm $INDICATOR_FILE
  while [[ $(aws glue get-job-runs --job-name ddoa-qa-dr-opcode.py --max-items 1 --region us-east-1)  = *"RUNNING"* ]]
    do
    sleep 1m
  done
  if [[ $(aws glue get-job-runs --job-name ddoa-qa-dr-opcode.py --max-items 1 --region us-east-1) = *"FAILED"* ]]; then
    echo "Job failed. Moving files to Backlog folder"
    aws s3 mv $INCOMING_FILES"CatCodeDelta.csv" $BACKLOG_FILES"CatCodeDelta$TIMEDATE.csv"
    wait ${!}
    aws s3 mv $INCOMING_FILES"OpCodeDelta.csv" $BACKLOG_FILES"OpCodeDelta$TIMEDATE.csv"
    wait ${!}
    aws s3 mv $INCOMING_FILES"OpCodeDetailDeltaLexus.csv" $BACKLOG_FILES"OpCodeDetailDeltaLexus$TIMEDATE.csv"
    wait ${!}
    aws s3 mv $INCOMING_FILES"OpCodeDetailDeltaToyota.csv" $BACKLOG_FILES"OpCodeDetailDeltaToyota$TIMEDATE.csv"
    wait ${!}
    aws s3 mv $INCOMING_FILES"CatCodeFull.csv" $BACKLOG_FILES"CatCodeFull$TIMEDATE.csv"
    wait ${!}
    aws s3 mv $INCOMING_FILES"OpCodeDetailFullLexus.csv" $BACKLOG_FILES"OpCodeDetailFullLexus$TIMEDATE.csv"
    wait ${!}
    aws s3 mv $INCOMING_FILES"OpCodeDetailFullToyota.csv" $BACKLOG_FILES"OpCodeDetailFullToyota$TIMEDATE.csv"
    wait ${!}
    aws s3 mv $INCOMING_FILES"OpCodeFull.csv" $BACKLOG_FILES"OpCodeFull$TIMEDATE.csv"
    wait ${!}
    echo "Remove source files in S3"
    aws s3 rm $INCOMING_FILES  --recursive
    exit 0
  fi
  echo "Archive csv files"
  aws s3 mv $INCOMING_FILES"CatCodeDelta.csv" $ARCHIVE_FILES"CatCodeDelta$TIMEDATE.csv"
  wait ${!}
  aws s3 mv $INCOMING_FILES"OpCodeDelta.csv" $ARCHIVE_FILES"OpCodeDelta$TIMEDATE.csv"
  wait ${!}
  aws s3 mv $INCOMING_FILES"OpCodeDetailDeltaLexus.csv" $ARCHIVE_FILES"OpCodeDetailDeltaLexus$TIMEDATE.csv"
  wait ${!}
  aws s3 mv $INCOMING_FILES"OpCodeDetailDeltaToyota.csv" $ARCHIVE_FILES"OpCodeDetailDeltaToyota$TIMEDATE.csv"
  wait ${!}
  aws s3 mv $INCOMING_FILES"CatCodeFull.csv" $ARCHIVE_FILES"CatCodeFull$TIMEDATE.csv"
  wait ${!}
  aws s3 mv $INCOMING_FILES"OpCodeDetailFullLexus.csv" $ARCHIVE_FILES"OpCodeDetailFullLexus$TIMEDATE.csv"
  wait ${!}
  aws s3 mv $INCOMING_FILES"OpCodeDetailFullToyota.csv" $ARCHIVE_FILES"OpCodeDetailFullToyota$TIMEDATE.csv"
  wait ${!}
  aws s3 mv $INCOMING_FILES"OpCodeFull.csv" $ARCHIVE_FILES"OpCodeFull$TIMEDATE.csv"
  wait ${!}
  echo "Remove source files in S3"
  aws s3 rm $INCOMING_FILES  --recursive
  #
  # We define file names
  #
  echo "Operation Code Deltas Toyota"
  OCDT='OA-TOY-OCD_'$TIMEDATE'.xml'
  echo "Operation Code Deltas Lexus"
  OCDL='OA-LEX-OCD_'$TIMEDATE'.xml'
  echo "Operation Code Full Toyota"
  OCFT='OA-TOY-OCF_'$TIMEDATE'.xml'
  echo "Operation Code Full Lexus"
  OCFL='OA-LEX-OCF_'$TIMEDATE'.xml'
  #
  # We process OpCode deltas Toyota
  #
  echo "Copy Toyota's detail file from S3 to EC2"
  aws s3 cp $STAGE_DETAIL_TOY $WORKING_DIRECTORY --recursive --exclude "*" --include "run-*"
  echo "Rename the file to input-details-toyota.xml in EC2"
  mv run-* input-delta-toyota.xml
  echo "Execute XSLT transformation for Toyota deltas"
  #xsltproc --output $OCDT showLaborOperations-TY.xsl input-delta-toyota.xml
  java -Xms8000m -jar saxon9he.jar -t -tree:tinyc -s:input-delta-toyota.xml -xsl:showLaborOperations-TY.xsl -o:stage-delta-toyota.xml
  wait ${!}
  java -Xms8000m -jar saxon9he.jar -t -tree:tinyc -s:stage-delta-toyota.xml -xsl:removeemptytag.xsl -o:$OCDT
  wait ${!}
  rm stage-delta-toyota.xml
  #
  # We process OpCode deltas Lexus
  #
  echo "Copy Lexus' deltas file from S3 to EC2"
  aws s3 cp $STAGE_DETAIL_LEX $WORKING_DIRECTORY --recursive --exclude "*" --include "run-*"
  echo "Rename the file to input-delta-lexus in EC2"
  mv run-* input-delta-lexus.xml
  echo "Execute XSLT transformation for Lexus deltas"
  #xsltproc --output $OCDL showLaborOperations-LX.xsl input-delta-lexus.xml
  java -Xms8000m -jar saxon9he.jar -t -tree:tinyc -s:input-delta-lexus.xml -xsl:showLaborOperations-LX.xsl -o:stage-delta-lexus.xml
  wait ${!}
  java -Xms8000m -jar saxon9he.jar -t -tree:tinyc -s:stage-delta-lexus.xml -xsl:removeemptytag.xsl -o:$OCDL
  wait ${!}
  rm stage-delta-lexus.xml
  #
  # We process OpCode full Lexus
  #
  echo "Copy Lexus' full file from S3 to EC2"
  aws s3 cp $STAGE_FULL_LEX $WORKING_DIRECTORY --recursive --exclude "*" --include "run-*"
  echo "Rename the file to input-full-lexus in EC2"
  mv run-* input-full-lexus.xml
  echo "Execute XSLT transformation for Lexus deltas"
  #xsltproc --output $OCFL showLaborOperations-LX.xsl input-full-lexus.xml
  java -Xms9000m -jar saxon9he.jar -t -tree:tinyc -s:input-full-lexus.xml -xsl:showLaborOperations-LX.xsl -o:stage-full-lexus.xml
  wait ${!}
  java -Xms9000m -jar saxon9he.jar -t -tree:tinyc -s:stage-full-lexus.xml -xsl:removeemptytag.xsl -o:$OCFL
  wait ${!}
  rm stage-full-lexus.xml
  #
  # We process OpCode full Toyota
  #
  echo "Copy Toyota's full file from S3 to EC2"
  aws s3 cp $STAGE_FULL_TOY $WORKING_DIRECTORY --recursive --exclude "*" --include "run-*"
  echo "Rename the file to input-full-toyota.xml in EC2"
  mv run-* input-full-toyota.xml
  echo "Execute XSLT transformation for Toyota full"
  java -Xms9000m -jar saxon9he.jar -t -tree:tinyc -s:input-full-toyota.xml -xsl:showLaborOperations-TY.xsl -o:stage-full-toyota.xml
  wait ${!}
  java -Xms9000m -jar saxon9he.jar -t -tree:tinyc -s:stage-full-toyota.xml -xsl:removeemptytag.xsl -o:$OCFT
  wait ${!}
  rm stage-full-toyota.xml
  #
  # Process zipping the files up.
  #
  echo "Zip the files up"
  #tar -czf $OCDT.gz $OCDT
  FILESIZE=$(stat -c%s $OCDT)
  if ((FILESIZE > 0 ));then
  gzip -c $OCDT > $OCDT.gz
  aws s3 cp $OCDT.gz $OUTGOING_FILES --content-type application/x-gzip
  wait ${!}
  aws s3 cp $OCDT.gz $OUTPUT_FILES --content-type application/x-gzip
   wait ${!}
  fi

  FILESIZE=$(stat -c%s $OCDL)
  if ((FILESIZE > 0 ));then
  gzip -c $OCDL > $OCDL.gz
  wait ${!}
  aws s3 cp $OCDL.gz $OUTGOING_FILES --content-type application/x-gzip
  wait ${!}
  aws s3 cp $OCDL.gz $OUTPUT_FILES --content-type application/x-gzip
  wait ${!}
  fi

  FILESIZE=$(stat -c%s $OCFT)
  if ((FILESIZE > 0 ));then
  gzip -c $OCFT > $OCFT.gz
  wait ${!}
  aws s3 cp $OCFT.gz $OUTGOING_FILES --content-type application/x-gzip
  wait ${!}
  aws s3 cp $OCFT.gz $OUTPUT_FILES --content-type application/x-gzip
  wait ${!}
  fi

  FILESIZE=$(stat -c%s $OCFL)
  if ((FILESIZE > 0 ));then
  gzip -c $OCFL > $OCFL.gz
  wait ${!}
  aws s3 cp $OCFL.gz $OUTGOING_FILES --content-type application/x-gzip
  wait ${!}
  aws s3 cp $OCFL.gz $OUTPUT_FILES --content-type application/x-gzip
  wait ${!}
  fi

#   sshpass -p "XSW@3edc" sftp ddoa_q@10.85.36.101 <<END_SCRIPT
#    cd ddoa/download/LaborOperations/TY/5.4.4.0.0
#    put $OCDT.gz
#    put $OCFT.gz
#    cd /
#    cd /home/ddoa/ddoa_q/ddoa/download/LaborOperations/LX/5.4.4.0.0
#    put $OCDL.gz
#    put $OCFL.gz
#    quit
# END_SCRIPT


  echo "Notify Lambda that the OpCode processed file is ready"
  sleep 1m
  md5sum $OCDT.gz > opcode-files-ready.dat
  wait ${!}
  md5sum $OCDL.gz >> opcode-files-ready.dat
  wait ${!}
  md5sum $OCFT.gz >> opcode-files-ready.dat
  wait ${!}
  md5sum $OCFL.gz >> opcode-files-ready.dat
  wait ${!}
  sed -i 's/  /,/g' opcode-files-ready.dat
  #sed -i 's/.gz//g' opcode-files-ready.dat
  aws s3 cp opcode-files-ready.dat $OUTGOING_FILES
  wait ${!}
fi
#rm -f opcode-files-ready.dat
echo 'End of OpCode process'
exit
```
