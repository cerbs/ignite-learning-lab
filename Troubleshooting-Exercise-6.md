# Exercise 6

**Method**: POST

**URL**: https://fhir-open.sandboxcerner.com/dstu2/0b8a0111-e8e6-4c26-a91c-5069cbc6b1ca/MedicationStatement

**Headers**: 
* Accept: application/json+fhir
* Content-Type: application/json+fhir

**Body**: 

    {
      "resourceType": "MedicationStatement",
      "contained": [
        {
          "resourceType": "Medication",
          "id": "123",
          "code": {
            "text": "FHIR Test Medication"
          }
        }
      ],
      "patient": {
        "reference": "Patient/1316024"
      },
      "status": "active",
      "medicationReference": {
        "reference": "#123"
      },
      "dosage": [
        {
          "timing": {
            "code": {
              "coding": [
                {
                  "system": "http://hl7.org/fhir/v3/vs/GTSAbbreviation",
                  "code": "BID"
                }
              ],
              "text": "BID"
            }
          },
          "quantityQuantity": {
            "value": 60.0,
            "units": "mg",
            "system": "http://unitsofmeasure.org",
            "code": "mg"
          }
        }
      ]
    }