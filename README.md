##**Upload & Store Geocoded Dataset with JQuery & PHP/MySQL**


### Introduction
-------------

This documentation demonstrates how to upload a Geocoded Address Data, Store the data in the Database and Display the Data to the User

When building a **Geocoded Address Database** you will need to extract the Geocoded data from a CSV file and store the data into a database for permanent storage. To accomplish this you will need to use a programming language to enable automated extraction of the data from CSV format and store them exactly as you want it into a Database.

In doing so, you might want to put some checks in place to ensure **reliability, authenticity and non-duplication** of data. Here we cover the basics, though a little extensively. You can also modify or add to this exposition in order to meet your desired needs.


----------


###Technologies Implemented
-------------

The following technologies have been used in this tutorial

 - *Programming Language (Server Side)* â€“ **PHP**
 - *Programming Language (Client Side)* â€“ JavaScript 
 - *Database* â€“ **MySQL** 
 - *Styling* â€“ **CSS3**
 - *Asynchronous Query Method* â€“ **JQuery Ajax**
 - *File Upload Method* â€“ **Jquery File Upload**
 - *Browser Support Detection* â€“ **HTML5**
 - *Form Object* â€“ **HTML5â€™s FormData object**
 - *Objects* - **JavaScript Objects**
 - *Cross Domain Request* â€“ **CORS (Cross-Origin Resource Sharing)**

-------------

###Specifications

-------------

####_**Unique Column**_
Uploading large volumes of _**Geo-coded**_ information or data onto a database will require _**high precision and accuracy**_ to ensure duplication is eliminated. Though when querying databases, specific query syntax such as _**DISTINCT**_ can be added to eliminate possibilities of duplicates, however, it will make no sense to have large volumes of repeated rows residing in the database table. 

Hence, specific computation column â€œlat_long_sumâ€ have been added and made _**UNIQUE**_ to the Database table **tbl_geo** to ensure that locations are not duplicated.

If you try to upload any same file containing the same data, during insertion into the database table, all the duplicate rows will be ignored.

-------------

###Display Uploaded Data To User

-------------

The Admin User that would upload the Geo-coded CSV data would love to see a sample of what he had uploaded. 

**Note: ** Any duplicate in the uploaded data displayed will be ignored when it is being stored in the database. This will be shown to the User.

-------------

###Implementing the Solution

-------------

####_**Workflow**_

In order to implement the file upload and database storage via ajax using _**PHP, JavaScript & JQuery**_, it is essential to think through the workflow. 

This would enable us to understand the process and enhance optimization in the future. The following are the workflow or step by step process.

- 	_Admin User navigates to the page containing the Form for Uploading Geo-coded CSV Data_ 
-	_The Admin User selects the file he wants to upload from the Systemâ€™s local folder and clicks send_
-	_The Form uses Ajax via to send remote request to the Server Side  PHP Script_
-	_The Server Side PHP Script processes the request by extracting CSV Data, Store the Data on the Database Server and then Displays the Data for the Admin User to see. The Script also computes the Unique variable to be used on the UNIQUE column._
-	_The Database Table Unique column ensures that duplicates are skipped during database storage_


####_**Resources**_

-------------

The resources used in the solution are as follows:

-	**_js/jquery-1.11.1.js_**
- 	_**js/js.js**_
-	_**asset/process.csvupload.php**_
-	_**css/css.css**_
-	_**./index.php**_

###**User Interace**

-------------

####_**Admin User Input HTML**_

-------------

#####_**./index.php**_
The HTML form is a **HTML5** compliant form which the Admin User uses to upload the Geo-coded CSV data. The form ```<form>``` is a **multi-part/form-data compliant form**. It must have a **POST** method attribute and an **enctype** method attribute. The id and name attributes of the form is **_iForm_**.

Below is the HTML of the Index Page:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />

<title>Geo-coding CSV Data File Reader</title>

<link rel="stylesheet" type="text/css" href="css/css.css">

<script language="javascript" src="js/jquery-1.11.1.js"></script>
<script language="javascript" src="js/js.js"></script>

</head>

<body>
	<div class="hdr">
		Geo-coding CSV Data File Reader	
	</div>
	
	
	<form id="iform" class="file_paragraph" action="" method="post" enctype="multipart/form-data" onsubmit="HandleUpload(event);">
		<table width="600" border="0" align="center" cellpadding="5" cellspacing="0">
			<tr>
				<td style="width:360px;"></td>
				<td>&nbsp;</td>
			</tr>
		  
			<tr class="file_row">
				<td align="left" valign="middle"  class="upload_title" id="selected-file">
					<span id="file_title">Select CSV File to Upload</span>
				</td>
				<td align="right" valign="middle">
					<input id="csv_file" class="csv_file" type="file" name="files[]" multiple="multiple" accept=".csv" /> 
					<input type="submit" name="Submit" value="Upload" />
				</td>
			</tr>
			<tr>
				<td width="363"></td>
				<td width="217">&nbsp;</td>
			</tr>
		</table>
	</form>
	
	<p>
		<div id="" class="">
			<div class="iprogress" id="iprogress">
				<img src="img/loading.gif" />
			</div>
			<div id="tbl_data" class="tbl_data">
				
			</div>
		</div>
	</p>

</body>
</html>

``` 

The HTML page index.php has been tested on both Firefox and the Chrome browsers. It may behave differently in other browsers.


###**Client Scripting**
-------------

####_**JAVASCRIPT (HANDLE UPLOAD)**_

------------

Letâ€™s examine what happens behind the scene on the _**Client Side**_ when the Admin User clicks on _**Upload**_ button. We will be working with _**JavaScript & JQuery Ajax**_ methods.

When the Admin User clicks the _**Upload**_ button, the ```HandleUpload()``` _**function**_ is called.  Below is the function which we will take time to explain chunk by chunk.
```
function HandleUpload(e){
	
	//PREVENT DEFAULT
	var evt = null;
	
	if(e){		
		evt = e;
	}else{
		evt = window.event;
	}
	evt.preventDefault();
	//**********************************
	//**********************************
	
	//SHOW PROGRESS
	obj.showProgress();
	//**********************************
	//**********************************
	
	//CLEAR PREVIOUS DATA
	$('#tbl_data').html('');
	//**********************************
	//**********************************
	
	//SET UPLOAD FILE URL
	var url = obj.UploadURL;
	//**********************************
	//**********************************
	
	if( obj.Upload != null ) {
			obj.Upload.abort();
			obj.Upload = null;
	}
	//**********************************
	//**********************************
	
	//GET THE FORM DATA OBJECT
	var form = document.forms.namedItem("iform");
	var formData = new FormData(form);
	//**********************************
	//**********************************
	
	//CHECK IF ANY FIELD IS EMPTY AND THEN SUBMIT
	setTimeout(function(){
		var length = iform.elements.length;
			
		var isEmpty = false;
		
		for(var i=0; i<length; i++){
			if(iform.elements[i].value == ""){
				isEmpty = true;		
			}
		}
		if(isEmpty){
			alert("Please select a file.");
			return false;
		}else{
			 
			
			$('#selected-file span').html(obj.Filename);
			
			//$('#tbl_data').html("<div style='text-align:center; font-family:arial; font-size:18px'>UpLoading..." + "</div>");
			//blink.do("tbl_data");
			
			setTimeout(function(){
				CallProcess(url, formData);
			}, 1000);		
		}
		
		
	}, 10);
	//**********************************
	//**********************************	
}
```

In order to perform Asynchronous Opertion or Ajax, the default **```onSubmit()```** event of the _**form**_ is suppressed using the **```event.preventDefault()```** method as seen below.

```
function HandleUpload(e){
	
	//PREVENT DEFAULT
	var evt = null;
	
	if(e){		
		evt = e;
	}else{
		evt = window.event;
	}
	evt.preventDefault();
	//**********************************
	//**********************************
```
In the next line of code the Javascript invokes the **```obj.showProgress()```** method which  displays the GIF file to show that a process is running.

```
obj.showProgress = function(){
	$('#iprogress').css("display","block");	
}
```
After the **```obj.showProgress()```** method is called we clear any previous data shown on the page to prepare the page for incoming data.

```
//CLEAR PREVIOUS DATA
	$('#tbl_data').html('');
	//**********************************
	//**********************************
```

In the next code we now set the Upload File PHP server script that would process the file to be uploaded.

```
//SET UPLOAD FILE URL
	var url = obj.UploadURL;
	//**********************************
	//**********************************
```

After setting the upload file we get the form data using the **```HTML5```** _FormData_ object seen below

```
//GET THE FORM DATA OBJECT
	var form = document.forms.namedItem("iform");
	var formData = new FormData(form);
	//**********************************
	//**********************************
```

Once the Form Data object is set we **validate** to check if any ***Form Data*** is empty. If any form data is empty, an error is thrown using the ***Javascript Alert Button***. If all form data is filled then we proceed on submitting the form using **JQuery Ajax**.

```
//CHECK IF ANY FIELD IS EMPTY AND THEN SUBMIT
	setTimeout(function(){
		var length = iform.elements.length;	
		var isEmpty = false;
		
		for(var i=0; i<length; i++){
			if(iform.elements[i].value == ""){
				isEmpty = true;		
			}
		}
		if(isEmpty){
			alert("Please select a file.");
			return false;
		}else{
			 
			$('#selected-file span').html(obj.Filename);
			
			setTimeout(function(){
				CallProcess(url, formData);
			}, 1000);		
		}
	}, 10);
	//**********************************
	//**********************************
}
```
------------

####***The CallProcess() Function***
The CallProcess function is the core function responsible for performing the JQuery Ajax query by sending the CSV File to the Server.

```
function CallProcess(url, formData){
	obj.Upload = $.ajax({
		url: url,			
		timeout: obj.Timeout,
		data: formData,
		type: 'POST',
		async: false,
		mimeType: "multipart/form-data",
		contentType: false,
		processData: false,
		beforeSend:function(data){
			
		},		
		success:function(data){
			var jsonData = $.parseJSON(data);
			if(jsonData.data.status == "SUCCESS"){
				DisplayData(jsonData.data.data);
			}else{
				var imsg 	= '<div style="text-align:center; font-size:14px; font-family:arial;">'; 
				imsg		+= "Error: " + jsonData.data.data+ '</div>'
				$('#tbl_data').html(imsg);
				obj.hideProgress();
				
			}
		},
		error:function(jqXHR, exception){	
			var error = obj.ajaxErrorHandler(jqXHR, exception);
			setTimeout(function(){
				var imsg 	= '<div style="text-align:center; font-size:14px; font-family:arial;">'; 
				imsg		+= error.title + ": " + error.info + '</div>'
				
				$('#tbl_data').html(imsg);
				obj.hideProgress();
			},200);
		}
	});	
}
```

Once the **Ajax Call** is successful, the **```onSuccess()```** handler of the JQuery Ajax displays the ***uploaded data*** to the Admin User using the **```onSuccess()```** function.

If an error is encountered, the **```onError()```** handler of the JQuery Ajax displays the error message to the user. The code is shown above.


###**Server Side Scripting**
----------

####_**PROCESS - PHP**_

----------

In order to process the file upload using the **PHP** Script, we purposely added an ***Cross-Origin Request Sharing (CORS)*** domain permission. If you are implementing this script and decide to Use absolute url for the script such as 

```http://abc.com/asset/UploadScript.php```

instead of the relative URL

```asset/ UploadScript.php```

then the Cross-Origin Request Sharing permission becomes critical otherwise your script will encounter error. That, said, letâ€™s see how our **PHP** Script header will look like.

```
<?php
//CROSS DOMAIN REQUEST ACCESS CONTROL
header('Access-Control-Allow-Origin: *');
error_reporting(0);
//********************************************
//********************************************
```

Once access control and headers are set, we define ***File Formats*** and ***maximum file size*** for the Script to process. If the file size is exceeded, the script will throw an error.

```
//FORMATS OF THE UPLOAD FILE
$valid_formats = array("csv");
$max_file_size = 1024*100000; //100 kb
//********************************************
//********************************************
```

Having Set the file formats we proceed to file upload process. Any error causes the upload to be aborted and response is receive by the Ajax request. First, the code iterates through all the files selected. In this case, only one file. 

**Validation:** The Script then skips any file that has an error. After, it validates if the file size being uploaded is larger than predefined allowed file sizes. Next all files that are not the right format are removed. 

However, in this case, the file field in the form has already defined the type of files to be selected, namely, CSV file format. When all validation is successful, the script then stores the uploaded CSV data to the database table using the **```InsertStmt()```** function and sends response data to the user for display using the **```DisplayData()```** function.


```
$message = "";

//HANDLE FILE UPLOAD
if(isset($_POST) and $_SERVER['REQUEST_METHOD'] == "POST"){
	// Loop $_FILES to exeicute all files
	foreach ($_FILES['files']['name'] as $f => $name) {     
	    if ($_FILES['files']['error'][$f] == 4) {
	        continue; // Skip file if any error found
	    }	       
	    if ($_FILES['files']['error'][$f] == 0) {	           
	        if ($_FILES['files']['size'][$f] > $max_file_size) {
	            $message = "$name is too large!.";
	            continue; // Skip large files
	        }
			elseif( !in_array(pathinfo($name, PATHINFO_EXTENSION), $valid_formats) ){
				$message = "$name is not a valid format";
				continue; // Skip invalid file formats
			}
	        else{ 
				// No error found! Store in Database 
				$file = fopen($_FILES["files"]["tmp_name"][$f],"r");
		
				$datar = DisplayData($file);
				$iDat = array();
				$iDat["data"]["status"] = "SUCCESS";
				$iDat["data"]["data"] = $datar;
				
				InsertStmt($datar);
	        }
	    }
		
	}
	
	if($message != ""){
		//IF ERROR
		$iDat = array();
		$iDat["data"]["status"] = "FAILED";
		$iDat["data"]["data"] = $message;;
		echo json_encode($iDat);
		//************
		//************
	}else{		
		//IF SUCCESS
		echo json_encode($iDat);
		//************
		//************
	}
}else{
	echo "hm";
}
//************
```

**```DisplayData()```** ***function*** is used to generate the data to be displayed to the user. The uploaded CSV File data is iterated row after row to read all data. The data read is stored in an array which is later converted to a JSON file for response back to the user. 

An extra column is created called ***lat_lng_sum*** which is a summation of the latitude and longitude to produce a unique number which will ensure that no two rows have the same value.

```
function DisplayData($file){
	$row = array();
	$count = 0;
	while(!feof($file)){	
		$file_row = fgetcsv($file);
		
		//make sure the header is not added
		if($file_row != null){
			$row[$count]["Place"] = $file_row[0];
			$row[$count]["Latitude"] = $file_row[1];
			$row[$count]["Longitude"] = $file_row[2];
			$row[$count]["StreetNo"] = $file_row[3];
			$row[$count]["StreetName"] = $file_row[4];
			$row[$count]["Address"] = $file_row[5];
			$row[$count]["Municipal"] = $file_row[6];
			$row[$count]["City"] = $file_row[7];
			$row[$count]["State"] = $file_row[8];
			$row[$count]["PostCode"] = $file_row[9];
			$row[$count]["Country"] = $file_row[10];
			$row[$count]["ISO3166_2"] = $file_row[11];
			$row[$count]["ISO3166_2_SUB"] = $file_row[12];
			$row[$count]["Category"] = $file_row[13];
		}
	
		$count++;
	}
	//$dat = DisplayTable($row);
	//return $dat;
	return $row;
}
```

The **```InsertStmt()```** ***function*** is used to insert the uploaded CSV File into the database. The **```InsertStmt()```** receives the array variable generated by the **```DisplayData()```** ***function*** and then using the data, a series of insert statements are generated. 

The array is iterated row after row as a series of insert statements are generated. When all statements have been prepared, the statements are executed.

```
function InsertStmt($arr){
	$len = count($arr);
	
	$conn = dbiConnect();
	if ($conn->connect_error) {
		die("Connection failed: " . $conn->connect_error);
	}
	
	$sql = "insert into tbl_geo (place, latitude, longitude, lat_long_sum, street_number, street_name, address, municipal, city, state, postcode, country, iso31662, iso31662sub, geohash, signature, category) values (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)";
	
	$stmt = $conn->prepare($sql);
	
	$stmt->bind_param("sdddsssssssssssss", $place, $latitude, $longitude, $lat_long_sum, $streetno, $streetname, $address, $municipal, $city, $state, $postcode, $country, $iso31662, $iso31662sub, $geohash, $signature, $category);
	
	for($i=0; $i<$len; $i++){
		if($i>0){
			$row = $arr[$i];
			$place = $row["Place"];
			$latitude = doubleval($row["Latitude"]);
			$longitude = doubleval($row["Longitude"]);
			$lat_long_sum = $latitude + $longitude;
			$streetno = $row["StreetNo"];
			$streetname = $row["StreetName"];
			$address = $row["Address"];
			$municipal = $row["Municipal"];
			$city = $row["City"];
			$state = $row["State"];
			$postcode = $row["PostCode"];
			$country = $row["Country"];
			$iso31662 = $row["ISO3166_2"];
			$iso31662sub = $row["ISO3166_2_SUB"];
			$geohash = md5(serialize($row));
			$signature = "BI." . strtoupper(substr(md5($geohash),0,8));
			$category = $row["Category"];
			
			$stmt->execute();
		
		}
	}
	
	if(mysqli_error($conn) == ""){
		$stmt->close();
		$conn->close();
		//echo "SUCCESS";
	}else{
		return mysqli_error($conn);
	}
}
```
Finally, the File Upload Script sends response back to the JQuery Ajax that displays the response to the user. 

------------

####**Storage**
------------

####_**THE DATABASE TABLE**_

------------

The Database Table is shown below. Run this script to install the database table to your database.

```
CREATE TABLE `tbl_geo` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `place` varchar(100) DEFAULT NULL,
  `latitude` double(20,7) DEFAULT NULL,
  `longitude` double(15,7) DEFAULT NULL,
  `lat_long_sum` double(15,7) DEFAULT NULL,
  `street_number` varchar(20) DEFAULT NULL,
  `street_name` varchar(100) DEFAULT NULL,
  `address` varchar(300) DEFAULT NULL,
  `municipal` varchar(100) DEFAULT NULL,
  `city` varchar(100) DEFAULT NULL,
  `state` varchar(100) DEFAULT NULL,
  `postcode` varchar(20) DEFAULT NULL,
  `country` varchar(100) DEFAULT NULL,
  `iso31662` varchar(20) DEFAULT NULL,
  `iso31662sub` varchar(20) DEFAULT NULL,
  `category` varchar(50) DEFAULT NULL,
  `date_added` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `date_updated` datetime DEFAULT NULL,
  `updated_by` varchar(20) DEFAULT 'Peter Edwards',
  `signature` varchar(15) DEFAULT NULL,
  `activated` varchar(20) DEFAULT 'ACTIVE',
  `geohash` varchar(50) DEFAULT NULL,
  `default_pic` varchar(100) DEFAULT NULL,
  `default_pic_caption` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `lat_long_sum` (`lat_long_sum`),
  UNIQUE KEY `lat_long_sum_2` (`lat_long_sum`),
  UNIQUE KEY `lat_long_sum_3` (`lat_long_sum`)
) ENGINE=InnoDB AUTO_INCREMENT=1046 DEFAULT CHARSET=latin1;
```

###Summary

------
We have built a simple page that will help an Admin User to Upload A CSV File containing Geo-coded information and then store that information into the database and consequently display the information to the user.

Critical specifications require that the Form be a Multipart Form Data in order for it to work. Secondly, the PHP Server Side script to upload the file implements a Cross-Origin Request Sharing protocol in order to ensure that accessed is granted to the Ajax Method calling the Server Side PHP Script.

Thirdly, the PHP Server Side script checks to ensure that the file size is within the allowed range, otherwise, the file will be rejected. 

And finally, a specific Computed UNIQUE column is implemented at the PHP Server Side Code level and at the Database table level to ensure that duplicates are eliminated during database storage.

The Codes are available using the following links
a.	js/jquery-1.11.1.js
b.	js/js.js
c.	asset/process.csvupload.php
d.	css/css.css
e.	./index.php
f.	asset/PointLevelGeoCoding20160621v2.csv

Download all the files [here](#).


