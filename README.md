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

