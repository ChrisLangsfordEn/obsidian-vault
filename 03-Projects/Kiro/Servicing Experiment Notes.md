1. Steering Files:
	1. Establish standards & steering files for 
		- [ ] IPG: 
			- [ ] insureworx-service
			- [ ] policy-service
			- [ ] spring Gateway?
		- [ ] IWX:
			- [ ] IWX-Servicing
			- [ ] iwx-configs
2. Requirements/Design/Tasks:
	1. Write up tickets as tasks
	2. Monitor Task Execution
	3. Compare with ISE's

**Note**: clone all relevant repos into an "agent" working directory for these changes. Open the entire directory, and allow Kiro to work from there (selecting the appropriate repositories for changes).



### Jira Tickets

| Jira Ticket                                    | Title                                                                                             | Repositiory              |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------- | ------------------------ |
| https://1jira.fnb.co.za/browse/INSUREWORX-1349 | [Servicing P&R] Implement Main Process                                                            | iwx-config,iwx-servicing |
| https://1jira.fnb.co.za/browse/INSUREWORX-1350 | [Servicing P&R] Implement Duplicate Check Component                                               | iwx-config               |
| https://1jira.fnb.co.za/browse/INSUREWORX-1351 | [Servicing P&R] Implement View Pending Task Component                                             |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1352 | [Servicing P&R] Implement Discard Pending Task & Resume Pending Task Component                    |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1353 | [Servicing P&R] Implement Retrieve Policy Details Component                                       |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1354 | [Servicing P&R] Implement Maintain Payment Details Component                                      |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1355 | [Servicing P&R] Implement XContract SubProcess Component                                          |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1356 | [Servicing P&R] Implement Update Payment Details Component                                        |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1357 | [Servicing P&R] Implement Condition Configurations on Debit Order Management Sub-Process          |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1358 | [Servicing P&R] Implement Condition Configurations on Policy Reinstatement Processing Sub-Process |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1359 | [Servicing P&R] Enhance the XML Generation for XContract                                          |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1360 | [Servicing P&R] Implement the Workflow Management Component                                       |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1361 | [Servicing P&R] Implement Comms Subprocess                                                        |                          |
| https://1jira.fnb.co.za/browse/INSUREWORX-1362 | [Servicing P&R] Implement Reinstate Plan Component                                                |                          |


| Use case name                    | Use Case                                                                                                                                                  |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Edit Payment Details             | As A User, I want to be able to initiate the Edit Payment Details and Activate Premium status functionalities on App and AoP.                             |
|                                  | As a User, I want to be able to see if the current debit order is still tracking.                                                                         |
|                                  | As a User, I want to be able to edit the Payment Details on a Funeral Plan.                                                                               |
|                                  | As a User, I want to be able to select add a non FNB account for debit order payments.                                                                    |
|                                  | As a User, I want the system to do a soft Hyphen Validation to check if the banking details provided are correct and be notified if changes are required. |
|                                  | As a User, I want to be notified if the debit order is currently in tracking.                                                                             |
|                                  | As a User, I want to be able confirm the changes made before the System updated the details.                                                              |
|                                  | As a User, I want to be able to view and accept my xContract before the changes are made.                                                                 |
|                                  | As a User, If the changes were made on AoP, I would like to trigger 2FA approval to the client to accept the contract before changes are made.            |
|                                  | As a User, If the changes were made on AoP, I would like to be notified after the Client has authorized the request                                       |
|                                  | As a User, I would like to be informed after the request has been submitted successfully.                                                                 |
|                                  | As an Insurer, I want the System to run a full Hyphen Verification Process for all Non-FNB Account.                                                       |
|                                  | As an Insurer, I want the System to create a workflow transaction on FLOW for all failed Hyphen Validation checks and PAS updates                         |
|                                  | As an Insurer, I want the System to create a workflow item if the Client requests a call back.                                                            |
|                                  | As a User, I want to be able to view comments to identify the reason for the Flow transaction                                                             |
|                                  | As An Insurer, I want the backend System to be able to cater for Multiple Policy/Plan Updates in one flow.                                                |
|                                  | As an Insurer, I want to be able to send notifications to clients after all successful updates and any failed requests                                    |
|                                  | As an Insurer, I want to trigger an endorsement letter at the back of each successful update.                                                             |
| <br>Policy Landing – Lapsed Plan | As a User, I want to be able to access my lapsed plan and initiate requests on the Policy Landing screen.                                                 |
| Reinstate Plan                   | As a User, I want to be able to view my Plan details before continuing to Reinstate                                                                       |
|                                  | As a User, I want to be able to view the terms of the Reinstatement                                                                                       |
|                                  | As a User, I want to be able to view and edit my Payment Details before the Reinstatement is completed                                                    |
|                                  | As a User, I want to be able to confirm my Plan Details before proceeding to contract for the Reinstatement                                               |
|                                  | As a User, I want to be able to read and accept a contract for the plan Reinstatement.                                                                    |
|                                  | As A User, I want to be able to pay arrears on the plan after reinstatement.                                                                              |
|                                  | As A User, I would like to be notified when my reinstatement is still in progress.                                                                        |
| <br>Permissions Requirements     | As an Insurer, I want to be able allocate and limit permissions to Reinstate and Edit Payment Details to certain AD Groups.                               |








