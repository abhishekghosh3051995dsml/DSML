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


attaching the refined output. The task actions (API / tool / agent) are now extracted against user intent, content and context for all 2,163 interactions.

How it's built

	•	Each interaction is rebuilt from NORA's own logs, step by step, in the order NORA actually ran them, using its timestamps.
	•	Duplicate log entries and late-logged agent calls are corrected first.
	•	Classes follow NORA's own domain label (category): business internet, home internet, signal & voice, data, roaming, messaging, etc.

Sheet 1 – Task Decomposition (one row per interaction)

	•	Interaction ID, Intent-L1 (L2/L4 left blank), User Content, Context (NORA's issue description)
	•	Task Class
	•	Task Actions in exact order (1, 2, 3…), plus the agents, tools and APIs called
	•	Extra tool / API calls, with the reason taken from NORA's log
	•	Anomaly flag (0/1)

Sheet 2 – Class Normal Path (ground truth per class)

	•	Normal steps NORA always runs, with agents and APIs, in order
	•	Accepted steps it adds regularly, with how often each happens

Key findings

	•	An interaction is flagged only if it runs a step outside both lists: about 8% of tool runs (125 of 1,573).
	•	Modify, query and ticket have no task actions in the logs (reply only).
