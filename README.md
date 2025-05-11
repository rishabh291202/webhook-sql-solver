import requests

# Step 1: Send POST request to generate webhook
url = "https://bfhldevapigw.healthrx.co.in/hiring/generateWebhook/PYTHON"
payload = {
    "name": "John Doe",
    "regNo": "REG12347",
    "email": "john@example.com"
}
response = requests.post(url, json=payload)

# Check the response
if response.status_code == 200:
    data = response.json()
    webhook_url = data.get("webhook")  # Submission URL
    access_token = data.get("accessToken")  # Token for authorization

    # Step 2: Determine question (REG12347 ends with 7 → Question 1)
    # Question 1 → Solve the SQL manually by reading the PDF
    final_query = """
    SELECT e.employee_id, e.name, d.department_name
    FROM employees e
    JOIN departments d ON e.department_id = d.department_id
    WHERE e.salary > (
        SELECT AVG(salary)
        FROM employees
    );
    """  # Example SQL – Replace with actual solution from the PDF

    # Step 3: Submit the SQL solution
    headers = {
        "Authorization": access_token,
        "Content-Type": "application/json"
    }
    submission_payload = {
        "finalQuery": final_query.strip()
    }

    submit_response = requests.post(webhook_url, headers=headers, json=submission_payload)

    if submit_response.status_code == 200:
        print("✅ SQL query submitted successfully!")
    else:
        print("❌ Failed to submit query:", submit_response.text)
else:
    print("❌ Failed to generate webhook:", response.text)
