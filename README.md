Get-Location

Get-Item ".\nora_entity_context_v4_1_1_final.py"
Get-Item ".\intent_labels_all_flattened.csv"

python --version

python -c "import pandas, numpy, scipy, sklearn, matplotlib, joblib, hdbscan, umap; print('CORE PACKAGES OK')"

python -c "import rdflib; print('RDFLIB OK')"

python -m pip install rdflib

python -m pip install pandas numpy scipy scikit-learn matplotlib joblib hdbscan umap-learn rdflib

python -m py_compile ".\nora_entity_context_v4_1_1_final.py"

python ".\nora_entity_context_v4_1_1_final.py" --help

python ".\nora_entity_context_v4_1_1_final.py" --mode train --profile fast_dev --input ".\intent_labels_all_flattened.csv" --outdir ".\nora_v411_smoke" --sample 1000

--ontology ".\some_ontology_file.ttl"

Get-Content ".\nora_v411_smoke\quality_gate.json"

Get-Content ".\nora_v411_smoke\selection_lock.json"

Start-Process ".\nora_v411_smoke\executive\nora_entity_context_v4_1_1_summary.html"

Import-Csv ".\nora_v411_smoke\semantic_cluster_catalog.csv" |
Format-Table cluster_id,count,share_of_assigned_pct,cohesion,descriptor -AutoSize
