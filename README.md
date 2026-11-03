## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT:

### DESIGN STEPS:

#### STEP 1:
Import the required libraries, load the API key, and define a Python function to calculate the volume of a cylinder using the formula for its volume.

#### STEP 2:
Define the function schema and provide it to the LLM through the chat completion API so that the model can identify when the function needs to be called.

#### STEP 3:
Execute the function using the arguments provided by the LLM, obtain the calculated volume, and send the result back to the LLM to generate the final response.

### PROGRAM:
import os
import json
import math
from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()
client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

def calculate_cylinder_volume(radius, height):
    """Calculate the volume of a cylinder."""
    return math.pi * radius ** 2 * height

tools = [{
    "type": "function",
    "function": {
        "name": "calculate_cylinder_volume",
        "description": "Calculate the volume of a cylinder",
        "parameters": {
            "type": "object",
            "properties": {
                "radius": {"type": "number"},
                "height": {"type": "number"}
            },
            "required": ["radius", "height"],
            "additionalProperties": False
        },
        "strict": True
    }
}]

messages = [{
    "role": "user",
    "content": "Calculate the volume of a cylinder with radius 3 and height 5."
}]

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=messages,
    tools=tools,
    tool_choice="auto"
)

message = response.choices[0].message
messages.append(message)

if message.tool_calls:
    for tool_call in message.tool_calls:
        if tool_call.function.name == "calculate_cylinder_volume":
            args = json.loads(tool_call.function.arguments)

            volume = calculate_cylinder_volume(
                args["radius"], args["height"]
            )

            messages.append({
                "role": "tool",
                "tool_call_id": tool_call.id,
                "content": str(volume)
            })

    final_response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages,
        tools=tools
    )

    print(final_response.choices[0].message.content)
else:
    print(message.content)

### OUTPUT:
<img width="802" height="456" alt="image" src="https://github.com/user-attachments/assets/73fcf562-237c-4f8c-90fb-c554fd169ad2" />


### RESULT:
Thus, the Python function for calculating the volume of a cylinder was successfully implemented and integrated with the LLM chat completion system using function-calling.
