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


Hi Sendil, I've updated the file as per your feedback. We use process mining and statistics on NORA's own logs (no LLM or prompting), so the same data always gives the same result.

Flow:

	1.	Input: each interaction starts with the user's full message and the category/subcategory they selected, both taken directly from the log. These are now the first columns of the Interactions sheet, along with NORA's reply and the feedback.
	2.	Standard plan: NORA's orchestrator first calls its context agents and APIs (subscription, device, location, outage and so on), then hands work to specialist agents (HSI, Billing, Data, Voice…).
	3.	Classes: interactions are grouped by which specialist agents the orchestrator hands work to (rule-based grouping). Fisher's exact test checks that each hand-off really brings specialist work. This gives 67 classes.
	4.	Class plan: learned from the order of calls in the logs (Heuristics Miner, a process mining algorithm). It's the standard plan plus each hand-off's sub-plan plus the class's own steps.
	5.	Decomposition: each interaction's steps are compared with its class plan, as shown in the Decomposition sheet.
	6.	New/extra calls: any call not in the class plan is an extra call. We check what the data showed just before it (the user's input, the category they selected, or an earlier API result) to find what triggered it (decision mining, with Fisher's exact test). For example, get_pgw_udm ← qtm_cellsector_ref_query: has_open_site_ticket = false.
	7.	Sub-classes (variants): extra calls with a proven trigger, made by at least 10% of a class, split the class into variants (trace clustering based on decision mining). For example, Class 01-A/B/C.
	8.	Repeats: when the same API is called many times, we compare the inputs of those calls to find what drives it. For example, search_socs ×15 makes one call for each SOC code returned earlier. An identical input each time means a retry.

  

