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


	#####


	
python .\nora_agent_summary.py --book ".\task_decomposition_version_1_8_3\nora_task_decomposition_version_1_8_3.xlsx"


Hi Sendil, here's the output and the findings from this method.

Technology: process mining and statistics on NORA's own logs. No LLM or prompting is used, so the same data always gives the same result.

Flow, input → output:
① Input from the logs: the user's message, the category they selected, what NORA recorded, NORA's reply and the feedback
→ ② Remove calls logged twice (record linkage)
→ ③ Each interaction's steps in time order, as agent → tool/API (process-mining event log)
→ ④ Check which specialist hand-offs really bring work (Fisher's exact test + Benjamini–Hochberg)
→ ⑤ Group cases by those hand-offs into 67 classes
→ ⑥ Learn the task plan and its order (Heuristics Miner, dependency ≥ 0.9)
→ ⑦ Mark every call outside the plan and find the data value that triggers it (decision mining)
→ ⑧ Split each class by those triggered calls into 89 variants (trace clustering)
→ ⑨ Explain every repeated call (input comparison)
→ ⑩ Show, API by API, why each case differs from a typical case (median profile)

Findings:

	•	NORA runs one core plan: 34 tasks in 5 stages (subscription, device, usage, provisioning, outage, location, SOC and history checks). On top of that come 11 specialist sub-plans and 174 class-specific steps. 93.6% of all calls are explained by this plan.
	•	11 of the 13 orchestrator hand-offs bring real specialist work: HSI, Billing, Data, Voice, Inseego, Roaming, Cradlepoint, Messaging, Digits, IoT and Wholesale. Sim_agent and Audit_agent run whether or not they're handed work.
	•	6.4% of calls are outside the plan. Half of them (3.2%) have a proven trigger in the data, covering 37 of the 56 extra-call types. For example:
	•	get_pgw_udm ← qtm_cellsector_ref_query: has_open_site_ticket = false;
	•	get_pgw_hss ← the LTE/5G attempts-and-drops query;
	•	get_keys ← category "T-Mobile Business Internet".
	•	Repeats drive most of the difference in call counts. A driver was found for 44 of the 56 APIs that repeat:
	•	search_socs runs once per SOC code;
	•	three snowflake queries frequently run twice (with and without numdays = 7);
	•	a few cases loop the same call hundreds of times.
	•	The user's input doesn't decide the path. Intent is always "clarification_needed" and the category is mostly "Other". The path depends on what NORA's tools return.
	•	Outcome differs by class. Cases with a specialist hand-off almost always finish the root cause. Cases with none (Class 01, 604) often don't: 330 have no completed root cause and 329 hit tool errors, mostly 401 Unauthorized.


These are the main observations. For every interaction, the sheet logs the exact call flow: each agent, tool and API called, in order, with its extra and repeated calls. Two issues stand out: (1) tool authorization errors (401 Unauthorized), concentrated in Class 01, where no specialist is handed the case; and (2) three snowflake queries that frequently run twice, once with numdays = 7 and once without, which adds avoidable calls. The rest of the detail is in the other sheets: Decomposition (steps per case), Task_Plan (the learned plan), API_Catalog (agents and APIs) and Classes / Class_Variants.
  

