import pandas as pd
import numpy as np
import random
from faker import Faker

fake = Faker()

# Number of records
num_records = 500000

# IDs ranges
num_patients = 20000
num_doctors = 2000
num_hospitals = 500
num_departments = 100
num_pharmacies = 300
num_suppliers = 800
num_medications = 2000

# Generate Fact_Medications
data = {
    "MedicationFactID": range(1, num_records + 1),
    "PatientID": np.random.randint(1, num_patients + 1, num_records),
    "DoctorID": np.random.randint(1, num_doctors + 1, num_records),
    "HospitalID": np.random.randint(1, num_hospitals + 1, num_records),
    "DepartmentID": np.random.randint(1, num_departments + 1, num_records),
    "PharmacyID": np.random.randint(1, num_pharmacies + 1, num_records),
    "SupplierID": np.random.randint(1, num_suppliers + 1, num_records),
    "MedicationID": np.random.randint(1, num_medications + 1, num_records),
    
    # Medicine details
    "BrandName": [fake.company() for _ in range(num_records)],
    "MedicineCategory": np.random.choice(["Antibiotic", "Painkiller", "Antiviral", "Vaccine", "Supplement"], num_records),
    "ChemicalContent": [fake.word() for _ in range(num_records)],
    "Provider": [fake.company() for _ in range(num_records)],
    "ManufactureDate": [fake.date_between(start_date="-2y", end_date="today") for _ in range(num_records)],
    "ExpiryDate": [fake.date_between(start_date="today", end_date="+2y") for _ in range(num_records)],

    # Supplier details
    "SupplierName": [fake.company() for _ in range(num_records)],
    "ContactPerson": [fake.name() for _ in range(num_records)],
    "ContactNumber": [fake.phone_number() for _ in range(num_records)],
    "Email": [fake.email() for _ in range(num_records)],
    "Address": [fake.address().replace("\n", ", ") for _ in range(num_records)],
    "City": [fake.city() for _ in range(num_records)],
    "State": [fake.state() for _ in range(num_records)],
    "Pincode": [fake.postcode() for _ in range(num_records)],
    "ContractEndDate": [fake.date_between(start_date="today", end_date="+3y") for _ in range(num_records)],

    # Price and Quantity
    "PricePerUnit": np.random.randint(10, 500, num_records),   # per unit price in INR
    "Quantity": np.random.randint(1, 50, num_records),         # number of doses purchased
}

# Create DataFrame
df = pd.DataFrame(data)

# Calculate Total Price
df["TotalPrice"] = df["PricePerUnit"] * df["Quantity"]

# Save to CSV
df.to_csv("Fact_Medications.csv", index=False)

print("✅ Fact_Medications.csv generated with 5 lakh records including Price, Quantity, and TotalPrice!")
