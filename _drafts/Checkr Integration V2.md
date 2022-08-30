## Checkr Integration V2

1. Set up the non-production test pipeline. 

2. support all kinds of checkr webhook events

3. support adjudication


## Paylocity Integration

1. help paylocity integration go to market. 

  - help customer to set up their account
  - write the doc for CS team.

2. Feature enhancement

  - support adding non-produciton account to production environment, so QA can test new feature on production.
  - allow customer to set up their paylocity account in the company integraiton list.
  - cached the company codes in workstream's database and improved the performance.
  - SLA improvement: monitor all company's connectivity.




## Exploration

1. work closely with ModulusData to implement the first version integration.



## DevOps of Connectors

1. Implemented the CICD of connectors. when developers push code to the sandbox branch of the connector microservice. The test code will be run by Github and the code will be deployed. It significantly improved the productivity of the application developers.

2. Set up Jenkins job for production deployment

3. Set up Sentry to monitor exceptions and raise alerts.

4. Implemented the health check for connectors.

5. Set up statuspage.io page for connectors.


the 