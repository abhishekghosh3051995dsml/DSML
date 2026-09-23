nora_entity_context_v7_1_final.py

Get-ChildItem

python -m pip install numpy pandas scipy scikit-learn matplotlib joblib

python -c "import numpy,pandas,scipy,sklearn,matplotlib,joblib; print('All dependencies OK')"

python -m py_compile .\nora_entity_context_v7_1_final.py

python .\nora_entity_context_v7_1_final.py --mode train --profile fast_dev --input ".\intent_labels_all_flattened.csv" --outdir ".\nora_v71_smoke" --sample 500 --allow-builtin-rules --no-persist-model
