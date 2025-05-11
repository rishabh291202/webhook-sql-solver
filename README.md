import requests

# Step 1: Generate webhook
name = "Rishabh Shavare"
reg_no = "0827CY233D03"
email = "rishabhshavare230685@acropolis.in"

response = requests.post(
    "https://bfhldevapigw.healthrx.co.in/hiring/generateWebhook/PYTHON",
    json={
        "name": name,
        "regNo": reg_no,
        "email": email
    }
)

data = response.json()
webhook_url = data["webhook"]         # Save this URL
access_token = data["accessToken"]    # Save the access token

print("Webhook URL:", webhook_url)
print("Access Token:", access_token)

# Step 2: Final SQL query (PASTE YOUR QUERY HERE as a single string)
final_sql_query = """
SELECT 
    e1.EMP_ID,
    e1.FIRST_NAME,
    e1.LAST_NAME,
    d.DEPARTMENT_NAME,
    COUNT(e2.EMP_ID) AS YOUNGER_EMPLOYEES_COUNT
FROM 
    EMPLOYEE e1
JOIN 
    DEPARTMENT d ON e1.DEPARTMENT = d.DEPARTMENT_ID
LEFT JOIN 
    EMPLOYEE e2 ON e1.DEPARTMENT = e2.DEPARTMENT 
    AND e2.DOB > e1.DOB
GROUP BY 
    e1.EMP_ID, e1.FIRST_NAME, e1.LAST_NAME, d.DEPARTMENT_NAME
ORDER BY 
    e1.EMP_ID DESC
"""

# Step 3: Submit the final query
submission_response = requests.post(
    webhook_url,
    headers={
        "Authorization": access_token,
        "Content-Type": "application/json"
    },
    json={"finalQuery": final_sql_query}
)

# Step 4: Check response
if submission_response.status_code == 200:
    print("✅ Submission successful!")
else:
    print("❌ Submission failed:", submission_response.text)
