python ".\nora_decomposition_pipeline.py" --interactions "..\intent_labels_sample_for_understand_interactions 1.csv" --labels "..\clustered_user_messages_3_balanced.xlsx" --until 6
python -m pip install igraph --index-url https://pypi.org/simple --trusted-host pypi.org --trusted-host files.pythonhosted.org


######
python ".\nora_data_analysis_v1.py" --file "C:\Datascience\interactions.csv" --out ".\interactions_data_analysis"


##
python .\nora_task_decomposition_phase1_.py --file "C:\Datascience\interactions_clean.parquet"


##
python .\nora_task_decomposition_phase_02.py --file "C:\Datascience\interactions_clean.parquet" --out ".\task_decomposition_v5"


####

python .\nora_knowledge_graph_v2.py
start .\task_decomposition_v7\nora_knowledge_graph.html


######
python -c "import pandas as pd; d=pd.read_parquet(r'C:\Datascience\interactions_clean.parquet'); c=d[d['source']=='chat_history']; print(c.columns.tolist()); print(c.head(2).T.to_string()[:3000])"


#######

python .\nora_check_repeats.py --file "C:\Datascience\interactions_clean.parquet" --id 00a62ae2 --api qtm_provisioning_status_daily_query


######

Hi Sendil, attaching the refined output. Task actions (API / tool / agent) are now extracted against user intent, content and context for all 2,163 interactions.

	•	Sheet 1: each interaction with its L1 intent, user content, context, NORA domain class, and the exact ordered task actions (agent → tool → API), plus extra calls with their reason and an anomaly flag (0/1).
	•	Sheet 2: the ground-truth normal path per class.

Steps are ordered from NORA's own log timestamps, and the classes follow NORA's own category labels. About 8% of tool runs deviate from their class's normal path. Modify, query and ticket have no task actions (reply only).
