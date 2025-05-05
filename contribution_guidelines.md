
Example of a parameter in JSON format: 
 ``` 
{
    "name": "Energy decay threshold",   // Display name
    "id": "edt",                        // id to use in backend
    "type": "integer",                  // value type (integer / float / list / string)
    "display": "text",                  // UI element to display (text / slider / checkbox / radio)
    "min": 10,                          // Minimum value (set to null if no minimum)
    "max": 60,                          // Maximum value (set to null if no minimum)
    "default": 35,                      // Default value
    "step": 1,                          // Step between two valid values
    "startAdornment": "start",          // To display before value
    "endAdornment": "dB"                // To display after value (such as a unit)
}
``` 
