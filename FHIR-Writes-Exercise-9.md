# Exercise 9 - Appointment Creation

Create an appointment using the following body, filling in the TODOs with appropriate values.
* Ensure you receive a 201 Created response.
* Retrieve your Appointment by id to verify the contents

***

    {
       "resourceType":"Appointment",
       "slot":{
          "reference":"TODO"
       },
       "participant":[
          {
             "actor":{
                "reference":"TODO"
             },
             "required":"required",
             "status":"TODO"
          }
       ],
       "status":"TODO"
    }