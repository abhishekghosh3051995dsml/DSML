python ".\nora_decomposition_pipeline.py" --interactions "..\intent_labels_sample_for_understand_interactions 1.csv" --labels "..\clustered_user_messages_3_balanced.xlsx" --until 6
python -m pip install igraph --index-url https://pypi.org/simple --trusted-host pypi.org --trusted-host files.pythonhosted.org


>>

>>, sharing the NORA task decomposition results (100 interactions, Excel attached). Could you check if I'm on the right track, or if I'm missing or getting anything wrong?
What I did: broke each interaction into ordered sub-tasks (agent → tool/API → target → result), using only what NORA actually did, not the intent.
• D2 (families): kNN graph + Leiden community detection. 5 families (Voice/SIM/Billing, HSI, No specialist, etc.)
• D3 (plans inside each family): hierarchical clustering of step sequences. 8 plans (including Roaming and Messaging)<<
