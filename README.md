# Azure Functions Python Template

A clean template repository for creating Azure Functions using Python Programming Model V2.

## 🚀 Quick Start

1. **Clone this template repository**
2. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```
3. **Run locally**:
   ```bash
   func start
   ```
4. **Test your function**:
   ```bash
   curl "http://localhost:7071/api/new_event_webhook?name=TestUser"
   ```

## 📁 Project Structure

```
azure-function-template/
├── function_app.py          # Main function definitions (Programming Model V2)
├── requirements.txt         # Python dependencies
├── host.json               # Azure Functions host configuration
├── .funcignore             # Files to ignore during deployment
├── .gitignore              # Git ignore rules
└── README.md               # This file
```

## 🔧 Programming Model V2 Overview

This template uses Azure Functions **Programming Model V2** which provides:

- **Decorator-based function definitions** - No more function.json files needed
- **Type hints support** - Better IDE experience and code clarity
- **Simplified project structure** - All functions in one `function_app.py` file
- **Better local development** - Easier testing and debugging

### Current Example Function

The template includes a simple HTTP trigger function:

```python
import azure.functions as func
import logging

# Create the function app instance with authentication level
app = func.FunctionApp(http_auth_level=func.AuthLevel.FUNCTION)

@app.route(route="new_event_webhook")
def new_event_webhook(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    # Get name from query parameters or JSON body
    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    # Return personalized or default response
    if name:
        return func.HttpResponse(f"Hello, {name}. This HTTP triggered function executed successfully.")
    else:
        return func.HttpResponse(
             "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.",
             status_code=200
        )
```

## 📝 Adding Your Own Functions

### HTTP Trigger Function

Add HTTP trigger functions to `function_app.py`:

```python
@app.route(route="my_function", methods=["GET", "POST"])
def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Processing my function request.')
    
    # Your logic here
    
    return func.HttpResponse("Success!", status_code=200)
```

### Timer Trigger Function

Add scheduled functions:

```python
@app.timer_trigger(schedule="0 */5 * * * *", arg_name="myTimer", run_on_startup=True)
def timer_function(myTimer: func.TimerRequest) -> None:
    logging.info('Timer trigger function executed.')
    
    # Your scheduled logic here
```

### Queue Trigger Function

Add queue processing functions:

```python
@app.queue_trigger(arg_name="msg", queue_name="myqueue", connection="AzureWebJobsStorage")
def queue_function(msg: func.QueueMessage) -> None:
    logging.info(f'Queue message: {msg.get_body().decode("utf-8")}')
    
    # Your queue processing logic here
```

### Blob Trigger Function

Add blob storage trigger functions:

```python
@app.blob_trigger(arg_name="myblob", path="samples-workitems/{name}", connection="AzureWebJobsStorage")
def blob_function(myblob: func.InputStream) -> None:
    logging.info(f"Blob trigger executed. Blob: {myblob.name}, Size: {myblob.length} bytes")
    
    # Your blob processing logic here
```

## 🧪 Testing

### Local Testing

1. **Start the function locally**:
   ```bash
   func start
   ```

2. **Test HTTP functions**:
   ```bash
   # GET request with query parameter
   curl "http://localhost:7071/api/new_event_webhook?name=TestUser"
   
   # POST request with JSON body
   curl -X POST "http://localhost:7071/api/new_event_webhook" \
        -H "Content-Type: application/json" \
        -d '{"name": "JSONUser"}'
   
   # Test without parameters
   curl "http://localhost:7071/api/new_event_webhook"
   ```

3. **View logs**: Check the terminal running `func start` for execution logs

## 🚀 Deployment

### Prerequisites

1. **Install Azure CLI**:
   ```bash
   curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
   ```

2. **Install Azure Functions Core Tools** (if not already installed):
   ```bash
   npm install -g azure-functions-core-tools@4 --unsafe-perm true
   ```

### Deploy to Azure

1. **Login to Azure**:
   ```bash
   az login
   ```

2. **Create a Function App**:
   ```bash
   az functionapp create \
     --resource-group myResourceGroup \
     --consumption-plan-location westus \
     --runtime python \
     --runtime-version 3.9 \
     --functions-version 4 \
     --name myFunctionApp \
     --storage-account mystorageaccount
   ```

3. **Deploy your function**:
   ```bash
   func azure functionapp publish myFunctionApp
   ```

## 🔧 Configuration

### Local Development Settings

Create a `local.settings.json` file for local development (this file is ignored by git):

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    "FUNCTIONS_WORKER_RUNTIME": "python"
  }
}
```

### Host Configuration

The `host.json` file contains runtime configuration:

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "excludedTypes": "Request"
      }
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[4.*, 5.0.0)"
  }
}
```

## 📚 Programming Model V2 Key Features

### 1. Function App Instance
```python
# Create the main app instance
app = func.FunctionApp(http_auth_level=func.AuthLevel.FUNCTION)
```

### 2. Decorator-Based Triggers
```python
# HTTP Trigger
@app.route(route="function_name")

# Timer Trigger  
@app.timer_trigger(schedule="0 */5 * * * *", arg_name="myTimer")

# Queue Trigger
@app.queue_trigger(arg_name="msg", queue_name="myqueue", connection="AzureWebJobsStorage")

# Blob Trigger
@app.blob_trigger(arg_name="myblob", path="container/{name}", connection="AzureWebJobsStorage")
```

### 3. Type Hints
```python
def my_function(req: func.HttpRequest) -> func.HttpResponse:
    # Function implementation
```

### 4. No function.json Files
- All configuration is done in Python code using decorators
- Cleaner project structure
- Better version control and code review experience

## 📖 Best Practices

1. **Use descriptive function names** that reflect their purpose
2. **Add proper logging** for debugging and monitoring
3. **Handle errors gracefully** with try-catch blocks
4. **Use type hints** for better code clarity
5. **Keep functions focused** on a single responsibility
6. **Use environment variables** for configuration
7. **Test locally** before deploying to Azure

## 🔗 Useful Links

- [Azure Functions Python Developer Guide](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-python)
- [Azure Functions Programming Model V2](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-python?tabs=asgi%2Capplication-level)
- [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local)
- [Azure Functions Triggers and Bindings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings)

## 🤝 Contributing

1. Fork this repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
