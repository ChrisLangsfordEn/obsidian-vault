
## Summary
There was an audit finding that shows the business is experiencing delays in resolving payment rejections and processing of claim payments. Currently, both PAS systems create daily exception reports indicating unsuccessful payments.

The current process is manual, with a team leader assigning these cases to assessors who will create a FLOW work item for each case.
Each manual payment is attempted:
1. First, via the PAS.
2. Secondly via MCP400, if via the PAS fails.

## Solution

Exception file to be loaded into an MFT location.

Scheduled job to pick up these exceptions and create IWX Processes. 

IWX will retry exceptions via the PAS (will require both Mint and ALIS APIs)


We essentially need to facilitate the retry process and allow for the capture of changes to necessary fields and provide a monitored MS for assessors to address failed payments timeously.

FLOW to be used to keep an audit trail.

Comms to be run via the comms engine.


### Hackathon
- Basic MVP
- Set up IWX API module
- Set up incoming api
- Set up local environment
- IPG API for reading data
- API API for making payment
- Camunda Forms
- Input Scripts
- Acceptance Criteria -> Test cases
- Goal -> Working PoC


Meg & Rosh:
- Main Process Draft
- Existing sub-process components
- Incoming IWX API spec/structure
