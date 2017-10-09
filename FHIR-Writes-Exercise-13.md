# Exercise 13 - DocumentReference Creation

* Pre-requisites
  * Query for an available patient encounter
  * Choose a supported LOINC from the fhir.cerner.com documentation
  * Base64-encode the following xhtml, customizing as you wish

`<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"><html xmlns="http://www.w3.org/1999/xhtml"><head><title>code Learning Lab</title></head><body>TODO</body></html>`

* Create a DocumentReference using the body below, filling in the TODOs with appropriate values
  * Ensure you receive a 201 Created response

***

    {
       "resourceType":"DocumentReference",
       "subject":{
          "reference":"TODO"
       },
       "type":{
          "coding":[
             {
                "system":"http://loinc.org",
                "code":"TODO"
             }
          ]
       },
       "author":[
          {
             "reference":"Practitioner/2578010"
          }
       ],
       "indexed":"2017-10-05T18:00:00Z",
       "status":"current",
       "docStatus":{
          "coding":[
             {
                "system":"http://hl7.org/fhir/composition-status",
                "code":"final"
             }
          ]
       },
       "description":"TODO",
       "content":[
          {
             "attachment":{
                "contentType":"application/xhtml+xml;charset=utf-8",
                "data":"TODO"
             }
          }
       ],
       "context":{
          "encounter":{
             "reference":"Encounter/TODO"
          }
       }
    }