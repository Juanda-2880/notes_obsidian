
 (computacionnube20264-507322)$ curl -X POST \                      curl -X POST \
  -H "Authorization: Bearer $(gcloud auth print-identity-token)" \
  -H "Content-Type: application/json" \
  -d '{
    "customer_id": "TEST-002",
    "senior_citizen": false,
    "partner": true,
    "dependents": true,
    "tenure": 48,
    "monthly_charges": 70.5,
    "contract": "Two year",
    "payment_method": "Credit card (automatic)"
  }' \
  https://churn-api-820873399990.us-central1.run.app/predict
{"customer_id":"TEST-002","customer_risk_score":0.1652,"model_version":"u4-class-mdl-20260904-b","predicted_at":"2026-09-12T02:50:24.445253Z","requested_by":"dianaj","source":"api_single","input_file":null}juandau_2880@cloudshell:~

![[Untitled 1-1789181578702.webp]]



 (computacionnube20264-507322)$ curl -X POST \                      curl -X POST \
  -H "Authorization: Bearer $(gcloud auth print-identity-token)" \
  -H "Content-Type: application/json" \
  -d '{
    "customer_id": "TEST-003",
    "senior_citizen": false,
    "partner": false,
    "dependents": false,
    "tenure": 1,
    "monthly_charges": 105.5,
    "contract": "Month-to-month",
    "payment_method": "Electronic check"
  }' \
  https://churn-api-820873399990.us-central1.run.app/predict
{"customer_id":"TEST-003","customer_risk_score":0.9489,"model_version":"u4-class-mdl-20260904-b","predicted_at":"2026-09-12T02:51:15.786629Z","requested_by":"dianaj","source":"api_single","input_file":null}juandau_2880@cloudshell:~


![[Untitled 1-1789181597350.webp]]

