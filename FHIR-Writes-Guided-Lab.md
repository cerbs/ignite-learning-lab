# Code Learning Lab - Writes - Guided Lab

This lab will guide you through several scenarios to register a new patient with an encounter using POST and PATCH, manage their allergies and appointments using POST and PUT, and finally writing and retrieving a simple XHTML document.

## Prerequisites

1. Complete the [FHIR Core Concepts](FHIR-Core-Concepts) labs
2. Complete the [Authorization](https://github.com/cerner/ignite-learning-lab/wiki/Authorization-Materials) labs 
    * Labs 1-4 **minimum**
3. Download and install [Postman](https://www.getpostman.com/downloads/)
4. Save and import the lab's [Postman Collection](materials/FHIR-Writes-Guided-Lab.postman_collection.json)

### Working through the scenarios

As you look over the requests in these scenarios you'll find words wrapped in {{}}, such as {{token}}. These are Postman variables. The scenarios will tell you to do things like "set the authorization token" or "set the patient id". When you come across these you can either replace the {{\<field>}} directly with the new value, or better yet, [update the pre-defined collection variables](https://learning.getpostman.com/docs/postman/environments_and_globals/variables/#defining-collection-variables) in Postman. Updating the collection variables will automatically apply the values to your requests without modifying the requests directly. This is helpful if you want to run through the scenarios more than once with different values, or when you need to update your authorization token. It also means that changes made for one request will apply to all of the requests and save a lot of copy-pasting.

Each section will list the OAuth scopes necessary to complete the scenario. Before starting the scenario you should follow the authorization workflow to request tokens with the necessary scopes. Populate the {{token}} header variable with your token. Be sure to include the "Bearer ". Depending on how quickly you work through the scenarios you may start receiving 401 errors indicating that you need to refresh your authorization token.

## Patient registration with R4 Patch

A new patient needs to be registered into the system. In this section we'll walk through several requests to add a new patient, update some of their demographics, and create a new encounter. We'll be using the requests found in the "Patient Registration (R4)" section of the Postman collection

**OAuth Scopes**  
user/Patient.read  
user/Patient.write  
user/Encounter.read  
user/Encounter.write  

### Create a patient

First things first, we need to create a new patient. In Postman, open the "1. Create a patient" request. Notice that we are using the POST http method and will be sending our request to the base Patient url without a patient id. The system will generate that for us. 

1. Click the Headers tab and find the Content-Type header. It should already be set to "application/fhir+json". This is the expected content type when POSTing, PUTting, or PATCHing to R4 FHIR servers.

2. Still on the Headers tab, find the Authorization header. It should currently be set to "{{token}}". If you have set the {{token}} collection variable, leave this header value as is. Otherwise, replace the value with your Bearer token.

3. In the request body, find the {{patientFistName}} variable. Set this value to give your new patient a first name.

4. Click "Send" to create the patient

5. In the response section (the lower portion of the screen), switch to the Headers tab and find the Status, Location, and Etag values.   
    - The Status should be 201 Created which indicates the patient was successfully created
    - The Etag value should be W/"0". This represents the current version of the patient. Make a note of this value.
    - The Location header has the URL to your newly created patient. Make a node of the number at the end of the URL. This is your new patientId.

Let's take a look at our new patient.

1. Open the "2. Retrieve the patient" request. Notice that the URL has a {{patientId}} variable at the end. Set it to the patientId return in the POST response Location header.

2. Click "Send" to retrieve the patient

3. Looking over the returned patient response we can see that some values were set for us by the system. Specifically that an MRN was added. Unfortunately, it looks like we set the wrong state in our initial request. It's current "MI" when it should be "MO". 

### Fix the home address

We should fix that before anyone notices. Make a new of the address's "id" field. We're going to need that to cleanup our mess. 

1. Open the "3. Fix home address" request. Notice that the HTTP method we are going to use is PATCH and that there is still a patientId variable at the end of the URL. 

2. On the Headers tab notice that the Content-Type is "application/json-patch+json". This is the expected Content-Type for PATCH operations. 
   
3. Next, notice that the If-Match header has a value of {{patientVersion}}. Set this value to the weak Etag value with the current version of the patient (W/"0").

5. Finally, on the Body tab, notice that the PATCH body has a {{patientAddressId}} variable in a test operation. This is necessary to ensure that we are going to update the correct address. Set it to the value of the address's "id" noted previously. It should look something like "CI-2891896-0"

6. Click "Send"

7. In the response section, switch to the Headers tab and find the Status, and Etag Headers.
   - Status should be 200 OK
   - Etag should now be W/"1"

8. Use the "2. Retrieve the patient" request to retrieve the patient again and verify that the address state is now "MO"

### Add a new identifier

Now that we've fixed the home address, add another MRN identifier.

1. Open the "4. Add an identifier" request

2. Set The If-Match header's {{patientVersion}} value to the current patient version. (Ex: W/"1")

3. Set the Body's {{newPatientMRN}} to a new MRN value of your choice. 

4. Click "Send". If the request fails with a 400 Bad Request, your MRN has likely already been assigned to a different patient in the system. Set a different MRN different value and try again.

5. In the response section, switch to the Headers tab and find the Status, and Etag Headers.
   - Status should be 200 OK
   - Etag should now be something like W/"2"

We can now search for your patient using your newly created MRN identifier.

1. Open the "5. Find patient byy identifier" request.

2. Ensure that {{newPatientMRN}} value in the request identifier search parameter is set to the new MRN value you added to the patient

3. Click "Send"

4. In the response section, switch to the Body tab and find your new MRN identifier in the patient's identifiers list.

### Create an encounter for the patient

Our patient is created and up to date. Let's create an encounter for them.

1. Open the "6. Create an encounter" request

2. Set the Body's {{patientId}} value to your patient's Id

3. Click "Send"

4. In the response section, switch to the Headers tab and find the Status, Etag, and Location Headers.
   - Status should be 201 Created
   - Etag should now be W/"0"
   - Location will have the URL to the new encounter. Make a note of the id, it will be useful later.

## Manage Allergies with DSTU2

Having created a patient with an encounter using the DSTU2 endpoint, let's switch to DSTU2 to manage the patient's allergies. For this section we will be using requests in the "AllergyIntolerance (DSTU2)" folder.

**OAuth Scopes**  
user/AllergyIntolerance.read  
user/AllergyIntolerance.write  

### Create an allergy

Like with Patient and Encounter, we'll create the allergy using a POST request.

1. Open the "1. Create a new Allergy" request

2. Note that we will be POSTing to the AllergyIntolerance resource and that, like all POSTs, there is no id.

3. On the Headers tab, note that Content-Type has a value of "application/json+fhir" for DSTU2 rather than "application/fhir+json" for R4

4. Update the Authorization {{token}} value with the appropriate bearer token

5. Ensure the Body {{patientId}} is configured with your patient id

6. Click "Send"

4. In the response section, switch to the Headers tab and find the Status, Etag, and Location Headers.
   - Status should be 201 Created
   - Etag should be something like W/"7943733". Make a note of the version.
   - Location will have the URL to the new AllergyIntolerance. Make a note of the id.

Let's take look at the allergy we created

1. Open the "2. Allergy - Get an Allergy" request

2. Note that we will be GETting from the AllergyIntolerance resource and that there is an {{allergyId}} variable at the end of the URL

3. Set the allergyId with the id of the allergy you just created.

4. Click "Send"

5. In the response section, see the Body tab to view your newly created AllergyIntolerance

### Update the allergy

It turns out our patient is _very_ allergy to calamine. We need to update their record to reflect this.

1. Open the "3. Allergy - Update the criticality" request

2. Note that we will be PUTting to the AllergyIntolerance and that there is an {{alergyId}} variable at the end of the URL.

3. Set the allergyId with the id of our recently created allergy

4. On the Body tab you should find a nearly request body. Unlike with PATCH where we can focused updates to specific field, we need to send the _entire_ AllergyIntolerance as part of the PUT operation. Use the "2. Allergy - Get an Allergy" request to retrieve the allergy you want to update and copy the entire response body.

5. Paste the "2. Allergy - Get an Allergy" response body into the "3. Allergy - Update the criticality" request body.

6. In the request body, find the meta.versionId value and use it to set the request If-Match header with a weak ETag value (Ex: W/"7943733")

7. Also in the request body, find the criticality field and change it from "CRITL" to "CRITH"

8. Click "Send"

9. In the response section, switch to the Headers tab and find the Status, and Etag Headers.
   - Status should be 200 OK
   - Etag should now be something like W/"7943739"
   
## Schedule an Appointment with DSTU2

Sadly, our patient isn't feeling well and needs to have an appointment scheduled to get checked out. Let's schedule an appointment for them. For this section we will be using requests in the "Appointment (DSTU2)" folder.

**OAuth Scopes**  
user/Appointment.read
user/Appointment.write
user/Slot.read

### Find an available slot

Before we can schedule the appointment, we need to find an available time slot.

1. Open the "1. Appt - Find available slot" request.

2. Note that we will be GETting from the Slot resource and that there is an {{appointmentDate}} variable in the query string. Set this value to something like 2019-06 to retrieve all appointment availabilities for the practitioner at the specific location.

3. In the response body should be a list of available Slot resources. Find one you like and make a note of its id. We will use that to schedule the appointment

### Schedule the appointment

You have the slot id. Time to create the appointment.

1. Open the "2. Appt - Create appointment" request.

2. Note that we will be POSTing to the Appointment resource

3. In the request Body find the {{slotId}} variable and ensure it is set to the slot id you found with the "1. Appt - Find available slot" request.

4. While in the request Body find {{patientId}} and ensure it has been set to your patient id.

5. Click "Send"

6. In the response section, switch to the Headers tab and find the Status, Etag, and Location Headers.
   - Status should be 201 Created
   - Etag should be something like W/"0". Make a note of the version.
   - Location will have the URL to the new Appointment. Make a note of the id.

### Cancel the appointment

Good news! The patient is feeling better and wants to cancel your hard work. To cancel the appointment we'll need to PUT the entire Appointment body back to the server with the status set to "cancelled". 

1. Open the "3. Appt - Retrieve appointment" request

2. Note that we will be GETting from Appointment resource and that there is an {{appointmentId}} variable at the end of the URL. Set this value to the Appointment id returned in the Location response header returned when initially creating the appointment.

3. Click "Send"

4. In the response section, the Appointment body should be displayed. Copy it.

5. Open the "4. Appt - Cancel Appt" request

6. Ensure that the {{appointmentId}} value in the URL is set

7. Paste the Appointment from your clipboard into the request Body field

8. Update the Appointment.status field from "booked" to "cancelled"

9. Click "Send"

10. In the response section, switch to the Headers tab and find the Status, and Etag Headers.
   - Status should be 200 OK
   - Etag should now be something like W/"1"
   
## Write documents with DSTU2

One last thing that we need to do is write a document to the patient's record. For this section we will be using requests in the "DocumentReference (DSTU2)" folder.

**OAuth Scopes**  
user/DocumentReference.read
user/DocumentReference.write
user/Binary.read

### Write a new document

It is *vital* that the following document be written to the patient's record

**Document XHTML**
```xhtml
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head><title>Note</title></head>
<body>
Code Learning Lab!
</body>
</html>
```

1. Open the "1. DocumentReference - Create a document" request.

2. Note that we will be POSTing to the DocumentReference resource

3. In the request Body ensure that the {{patientId}} variable is set

4. Remember that encounter you created during patient registration? Now's its time to shine. In the request Body find the {{encounterId}} variable. Ensure that it is set to the encounter id you previously created for the patient.

5. Next you need to Base64 Encode the document XHTML listed above. https://www.base64encode.org/ is a good resource for that.
 
6. Finally, you need to update the content.\[0].attachment.data value with the Base64 encoded encoded document.

7. Click "Send"

8. In the response section, switch to the Headers tab and find the Status, Etag, and Location Headers.
   - Status should be 201 Created
   - Etag should be something like W/"0". Make a note of the version.
   - Location will have the URL to the new Document. Make a note of the id.

### Get the document

Let's see what we wrote. 

1. Open the "2. DocumentReference - Get the document back"

2. Note that the request URL has a {{documentId}} variable at the end. Set it to the id of the document you just wrote.

3. Click "Send"

4. The DocumentReference is returned, but the content is not. Instead find the URL at content[0].attachment.url. Make a note of the Binary id. It should be something like "XR-11049287"

5. Open the "3. DocumentReference - Get the Binary"

6. Note that the request URL has a {{binaryId}} variable at the end. Set it to the binary id returned in the DocumentReference

7. Click "Send"

8. The Binary is returned. The Binary.content field contains the base64 encoded version of the document as returned from Millennium.

9. Note that the Binary.contentType is "application/pdf"

10. Update the request Header Content-Type value from "application/json+fhir" to "application/pdf"

11. Click "Send"

12. This time the PDF is downloaded. (You may have to click the download button at the top-right of the Response section in Postman)