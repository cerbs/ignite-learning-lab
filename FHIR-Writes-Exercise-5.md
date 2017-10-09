# Exercise 5 - MedicationStatement Creation

Create a MedicationStatement using the following body, filling in the TODOs with appropriate values.
* Ensure you receive a 201 Created response
* Retrieve your MedicationStatement by id to verify the contents

***

    {
       "resourceType":"MedicationStatement",
       "patient":{
          "reference":"Patient/TODO"
       },
       "status":"TODO",
       "medicationCodeableConcept":{
          "text":"TODO"
       },
       "dosage":[
          {
             "timing":{
                "code":{
                   "coding":[
                      {
                         "system":"http://hl7.org/fhir/v3/vs/GTSAbbreviation",
                         "code":"BID"
                      }
                   ],
                   "text":"BID"
                }
             },
             "quantityQuantity":{
                "value":60.0,
                "units":"mg",
                "system":"http://unitsofmeasure.org",
                "code":"mg"
             }
          }
       ]
    }