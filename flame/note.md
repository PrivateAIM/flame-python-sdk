# flame-python-sdk

# Tasks:
1. Data source access
2. Result submission
3. Send meta information through the Node API to the message broker to other nodes
4. Federation Protocols Implementation

# Processes:
- read and check tokens for
  - message broker
  - data api
  - storage minio api
- send data to message broker
- listen to message broker
- access the data api 
- getting old information from the storage minio api
- protocols for federated execution
  - await parameter updates
  - update parameters
  - reexecution
  - check if aggregation node (specified in config)
  - aggregation function

# Communications
always over nginx (tba)\
Node address = (message broker address, analysis id)
### Message Broker
  0. receive message broker token
  1. get node type/(configuration)
     - send: project id, analysis id, token
     - receive: Analyzer/Aggregator mode (and other configurations)
  2. send message broker endpoint of analysis/aggregator
     - send: project id, analysis id, token
     - receive: success/fail
  3. get other node message addresses
     - send: project id, analysis id, token
     - receive: list of node addresses and their roles
  4. send message to other nodes
     - send: project id, analysis id, token, target node address, message
     - receive: acknowledgement
  5. send results to hub
     - send: project id, analysis id, token, results
     - receive: acknowledgement

### Data API
  0. receive data api source token
  1. get available data sources
     - send: project id, analysis id, token
     - receive: list of data sources
  2. get data from source
     - send: project id, analysis id, token, data source id
     - receive: data
  


# Workflows:
at startup check if (also) aggregation node 

### Analyzer node
1. read/check tokens (message broker, data api, storage api)
2. perform analysis
3. send parameters to message broker
4. if federated mode and not last epoch, await aggregated parameters
5. if federated mode and not last epoch go to 2.

### Aggregation node (if federated mode)
1. read/check tokens (message broker, storage api?)
2. await parameter updates 
3. aggregate parameters
4. send aggregated parameters to message broker
5. send final result to hub

# Project structure
flame\
|__flame.py (check execution mode: analysis/aggregation)\
|&emsp;|__class FlameSDK\
|&emsp;|__class FlameAggregator(.federated.aggregator_client.Aggregator)\
|&emsp;|__class FlameAnalyzer(.federated.analyzer_client.Analyzer)\
|\
|__utils\
|&emsp;|__token.py\
|&emsp;&emsp;&emsp;|__def read_token() / receive_token()\
|&emsp;&emsp;&emsp;|__def check_token() / test_token()\
|\
|__clients\
|&emsp;|__minio_client.py\
|&emsp;|__data_api_client.py\
|&emsp;|__message_broker_client.py\
|\
|__federated\
|&emsp;|__aggregator_client.py (for Aggregation node)\
|&emsp;|&emsp;|__class Aggregator()\
|&emsp;|&emsp;&emsp;&emsp;|__def execute()\
|&emsp;|&emsp;&emsp;&emsp;|__def _await_parameters()\
|&emsp;|&emsp;&emsp;&emsp;|__def _aggregate_parameters()\
|&emsp;|&emsp;&emsp;&emsp;|__def _send_parameters()\
|&emsp;|\
|&emsp;|__analysis_client.py (for Analysis node)\
|&emsp;&emsp;&emsp;|__class AnalysisClient()\
|&emsp;&emsp;&emsp;&emsp;&emsp;|__def send_results()\
|&emsp;&emsp;&emsp;&emsp;&emsp;|__def await_aggregation()\
|\
|__protocols\
|&emsp;|__aggregation_paillier.py\
|&emsp;&emsp;&emsp;|__def aggregate()\
|&emsp;&emsp;&emsp;|__def _load_keys.py\
|&emsp;&emsp;&emsp;|__def _encrypt()\
|&emsp;&emsp;&emsp;|__def _enc_add()\
|&emsp;&emsp;&emsp;|__def _invmod()\
|\
|__templates\
&emsp;&emsp;|__main_generic.py\
&emsp;&emsp;|__aggregator_generic.py\
&emsp;&emsp;|&emsp;|__def aggregate()\
&emsp;&emsp;|__analyzer_generic.py()\
&emsp;&emsp;&emsp;&emsp;|__def main()




