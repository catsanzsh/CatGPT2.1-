import openai
import json

# Set up your API key
openai.api_key = "YOUR_API_KEY"

# Define the model variants you want to support (placeholder names)
model_variants = ["o1-preview", "o1-mini", "o1", "o1-pro"]

# Prompt for generating 5 sentences in one go
prompt = """
Generate a JSON object with exactly 5 sentences under the key "sentences".
The JSON should look like this:
{
  "sentences": [
    "Sentence 1.",
    "Sentence 2.",
    "Sentence 3.",
    "Sentence 4.",
    "Sentence 5."
  ]
}
Return only JSON, with no extra text.
"""

for model_variant in model_variants:
    # Make the API call using the specified model variant
    response = openai.Completion.create(
        engine=model_variant,     # Use the current variant
        prompt=prompt,
        max_tokens=250,
        temperature=0.5,
        n=1,
        stop=["}"]
    )

    # Extract the raw text returned by the API
    raw_text = response['choices'][0]['text'].strip()

    # Attempt to form a complete JSON string by adding the closing bracket if missing
    if not raw_text.endswith('}'):
        raw_text += "}"

    # Parse the JSON
    data = json.loads(raw_text)

    # Write to a file named after the model variant for clarity
    filename = f"test_{model_variant}.json"
    with open(filename, 'w') as file:
        json.dump(data, file, indent=4)

    print(f"{filename} has been created with 5 sentences using {model_variant}.")
