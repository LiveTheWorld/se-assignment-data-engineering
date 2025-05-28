# Data Engineering Assignment: Chat Analytics Pipeline

## Overview

You'll build a Python pipeline that processes raw chat data containing embedded message logs, transforming it into structured CSV files for analytics.

## Business Context

Our chat platform generates raw chat data with embedded message logs in JSON format. We need to transform this data into two structured datasets:
- **Chat-level metrics** for conversation analysis  
- **Message-level data** for detailed interaction tracking

## Your Task

Create a Python script called `chat_pipeline.py` that:
1. Reads the provided `source_chat_data.csv` file
2. Extracts and normalizes the embedded message logs from the `conversation_log` field
3. Outputs two CSV files: `chats.csv` and `messages.csv`

## How to Create the Messages Table

The key challenge is **flattening the conversation log** from each chat into individual message rows:

### Step-by-Step Process:

1. **Parse the JSON**: Each chat has a `conversation_log` field containing a JSON string with an array of messages
2. **Extract each message**: Loop through the message array and create one row per message
3. **Inherit chat data**: Each message row gets the chat-level fields (chat_id, language, sentiment, etc.)
4. **Add message-specific data**: Include the message content, role (user/assistant), and message ID
5. **Handle missing fields**: Fill in empty values for fields not available in the source data

### Example Transformation:

**Input (1 chat row):**
```
chat_id: "chat_123"
language: "en"
conversation_log: '[{"id":"msg_1","role":"user","content":"Hello"},{"id":"msg_2","role":"assistant","content":"Hi there!"}]'
```

**Output (2 message rows):**
```
chatId,language,role,content,messageId,...
chat_123,en,user,Hello,msg_1,...
chat_123,en,assistant,Hi there!,msg_2,...
```

### Key Points:
- **One chat → Multiple messages**: A single chat with 5 messages becomes 5 rows in messages.csv
- **Shared data**: All messages from the same chat share the same chat-level information
- **Message-specific fields**: Only messages have `role`, `content`, and `messageId` fields
- **JSON parsing**: Use `json.loads()` to parse the `conversation_log` string into a Python list

## Getting Started

1. **Clone or download this repository**
2. **Install dependencies:** `pip install -r requirements.txt`
3. **Examine the data:** Look at `source_chat_data.csv` to understand the structure
4. **Create your solution:** Build `chat_pipeline.py`
5. **Test your solution:** Run `python chat_pipeline.py`
6. **Submit via email** when ready (see submission instructions below)

## Submission Instructions

When you're ready to submit your solution, please email it to the address provided in your assignment email with:

**Attachments:**
- Your Python script (`chat_pipeline.py`)
- Generated output files (`chats.csv` and `messages.csv`)
- Any additional files you created (if applicable)

**Email Body:** Please include a brief description of:
- Your approach to solving the problem
- Any challenges you encountered and how you solved them
- Instructions for running your script (if not standard)
- Any assumptions you made

## Available Data

### Source Data: `source_chat_data.csv`

Real production chat data (anonymized) with the following structure:
- **chat_id**: Unique chat identifier
- **slug**: Anonymized chat slug
- **token**: Anonymized chat token
- **organization_id**: Organization identifier
- **conversation_log**: JSON string containing array of message objects
- **structured_info**: Additional chat metadata (JSON)
- **created_at/updated_at**: Timestamps
- **language**: Chat language (nl, fr, en)
- **is_test_chat**: Boolean flag
- **sentiment/resolution**: Chat classification fields
- **message_count**: Number of messages in the conversation

### Message Log Structure

Each message in the `conversation_log` has:
```json
{
  "id": "unique-message-id",
  "role": "assistant|user",
  "type": "default|...",
  "content": "message text",
  "disclaimer": "optional disclaimer text"
}
```

## Expected Outputs

Your pipeline should generate two structured CSV files with the exact field structure shown below:

### chats.csv (47 fields)
```
locations,first name,user bypass messages limit,name,read only token,phone number,pois,is test chat,clientId,organizationConfigId,email,suborganization,provinces,starting domain,section type,is reviewed,limit messages case,sentiment,slug,is chat classified,ontraport update time,trip type,most relevant trip name,countries,chatId,email types,newsletter subscription time,language,last name,idle message sent time,resolution,token,subscription idle message sent time,is default chat,ontraport note id,actions,has email,most relevant brand name,created at,organizationId,recommendations cta url,updated at,idle email sent time,humanloop session id,defaultMessageId,is structured info extracted,cities
```

### messages.csv (47 fields)
```
locations,messageSuborganization,user bypass messages limit,current brands,read only token,pois,is test chat,clientId,organizationConfigId,email,suborganization,provinces,role,starting domain,is reviewed,limit messages case,sentiment,intentType,slug,is chat classified,ontraport update time,trip type,regions,countries,chatId,newsletter subscription time,conversationLogType,language,idle message sent time,resolution,sk messageId,token,subscription idle message sent time,is default chat,messageId,categories,content,ontraport note id,created at,organizationId,updated at,idle email sent time,humanloop session id,defaultMessageId,is structured info extracted,themes,cities
```

**Important Notes:**
- Your output files must have **exactly these field names** in **exactly this order**
- Fields not available in source data should be left empty or filled with appropriate defaults
- Some fields will need to be derived/calculated from the available source data
- Pay attention to field differences between chats and messages (e.g., `messageSuborganization` vs `suborganization`)
- The `role` field in messages should indicate whether the message is from `assistant` or `user`
- The `content` field in messages should contain the actual message text from the conversation log

## Array/List Field Format

Several fields expect **JSON-formatted arrays** as strings. Here's the required format:

### Empty Arrays
For fields with no data, use empty JSON arrays:
```
locations,pois,provinces,section type,trip type,email types,cities
,[],[],[],[],[],[]
```

### Non-Empty Arrays
For fields with data, use JSON array format with proper escaping:
```
actions,countries,locations
"[""get_in_touch""]","[""Netherlands"",""Belgium""]","[""Amsterdam"",""Brussels""]"
```

### Array Fields in chats.csv:
- `locations`, `pois`, `provinces`, `section type`, `trip type`, `countries`, `email types`, `actions`, `cities`

### Array Fields in messages.csv:
- `locations`, `pois`, `provinces`, `trip type`, `regions`, `countries`, `categories`, `themes`, `cities`

**Key Points:**
- Use `json.dumps()` in Python to properly format arrays
- Empty arrays should be `[]` (not empty strings)
- String values in arrays need double escaping in CSV: `"[""value""]"`
- Arrays can contain strings, numbers, or other JSON-compatible types

## Requirements

### Technical Requirements
- **Python 3.8+**
- **Use pandas** for data manipulation
- **Handle missing/null values** appropriately
- **Include basic error handling**
- **Add logging** to show processing progress

### Data Processing Requirements
- **Extract messages** from embedded JSON log data
- **Calculate meaningful metrics** from the available fields
- **Handle data quality issues** (duplicates, invalid values, etc.)
- **Extract time-based features** where applicable
- **Add data quality indicators**

### Code Quality Requirements
- **Clean, readable code** with appropriate comments
- **Modular functions** for different processing steps
- **Proper variable naming** and code organization
- **Include documentation** explaining your approach

## AI Usage Policy

**AI tools are welcome and encouraged!** You may use ChatGPT, Cursor, Claude, or any other AI assistance to help with your solution.

**However, you must be able to:**
- **Understand every line** of code you submit
- **Explain your design choices** and approach during review
- **Modify or debug** your code if asked
- **Answer questions** about how your solution works

**During the evaluation, you may be asked to:**
- Walk through your code and explain key functions
- Justify why you chose certain approaches over alternatives
- Debug or modify parts of your solution
- Explain how you handled edge cases or data quality issues


## Evaluation Criteria

Your solution will be evaluated on:
- **Functionality**: Does it work and produce meaningful outputs?
- **Code Quality**: Is it clean, readable, and well-organized?
- **Data Processing**: Does it handle edge cases and data quality issues?
- **Documentation**: Is your approach clearly explained?

## Time Expectation

This assignment should take **2-6 hours** to complete, depending on your experience level:

- **Junior (0-2 years)**: 4-6 hours
- **Mid-level (2-5 years)**: 2-5 hours  

**Why it takes time:**
- Understanding the complex JSON structure in `conversation_log`
- Mapping 13 source fields to 47 target fields with exact naming
- Handling edge cases (missing data, malformed JSON)
- Getting the field order exactly right
- Testing and debugging

Focus on getting a **working solution first**, then improve code quality and add features.

## Questions?

If you have questions about the requirements, feel free to reach out to the email address provided in your assignment instructions.

Good luck! 🚀 