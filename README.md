import pandas as pd
import numpy as np
import random
from faker import Faker

fake = Faker()

# Parameters
n_records = 500000
n_patients = 20000
n_doctors = 2000
n_hospitals = 500
n_pharmacies = 300
n_suppliers = 800
n_medicines = 1000

# Generate IDs
patient_ids = [f"P{str(i).zfill(5)}" for i in range(1, n_patients+1)]
doctor_ids = [f"D{str(i).zfill(4)}" for i in range(1, n_doctors+1)]
hospital_ids = [f"H{str(i).zfill(3)}" for i in range(1, n_hospitals+1)]
pharmacy_ids = [f"PH{str(i).zfill(3)}" for i in range(1, n_pharmacies+1)]
supplier_ids = [f"S{str(i).zfill(4)}" for i in range(1, n_suppliers+1)]
medicine_ids = [f"M{str(i).zfill(4)}" for i in range(1, n_medicines+1)]

# Pre-generate dictionaries for attributes
patients = {
    pid: {
        "Patient_Name": fake.name(),
        "Patient_Gender": random.choice(["Male", "Female"]),
        "Patient_DOB": fake.date_of_birth(minimum_age=10, maximum_age=90),
        "Patient_Address": fake.street_address(),
        "Patient_City": fake.city(),
        "Patient_State": fake.state(),
        "Patient_Country": fake.country(),
        "Patient_PostalCode": fake.postcode(),
        "Patient_Age": None,
        "Patient_SatisfactionScore": random.randint(1, 10),
        "Patient_BloodGroup": random.choice(["A+", "A-", "B+", "B-", "O+", "O-", "AB+", "AB-"]),
        "Patient_WaitTime": random.randint(5, 120)
    }
    for pid in patient_ids
}

# Fill derived Patient_Age
for pid, pdata in patients.items():
    dob = pdata["Patient_DOB"]
    age = 2025 - dob.year
    patients[pid]["Patient_Age"] = age

doctors = {
    did: {
        "DoctorFullName": fake.name(),
        "Doctor_Specialization": random.choice(["Cardiology","Neurology","Orthopedics","Pediatrics","General Physician","Oncology"]),
        "Doctor_Years_Experience": random.randint(1,40),
        "Doctor_Email": fake.email(),
        "Doctor_Shift_Type": random.choice(["Day","Night","Rotational"])
    }
    for did in doctor_ids
}

hospitals = {
    hid: {
        "Hospital_Name": fake.company() + " Hospital",
        "Hospital_Provider_Name": fake.company(),
        "Hospital_Type": random.choice(["Private","Government","Trust"]),
        "Bed_Capacity": random.randint(50,1000),
        "Hospital_Address": fake.street_address(),
        "Hospital_State": fake.state(),
        "Hospital_City": fake.city(),
        "Hospital_Country": fake.country(),
        "Hospital_PostalCode": fake.postcode()
    }
    for hid in hospital_ids
}

pharmacies = {
    phid: {
        "PharmacyName": fake.company() + " Pharmacy",
        "PharmacyLocation": fake.city(),
        "PharmacyContact": fake.phone_number()
    }
    for phid in pharmacy_ids
}

suppliers = {
    sid: {
        "SupplierName": fake.company(),
        "ContactPerson": fake.name(),
        "SupplierContact": fake.phone_number(),
        "SupplierEmail": fake.email(),
        "SupplierAddress": fake.street_address(),
        "SupplierCity": fake.city(),
        "SupplierState": fake.state(),
        "SupplierPincode": fake.postcode(),
        "ContractEndDate": fake.date_between(start_date="+30d", end_date="+730d")
    }
    for sid in supplier_ids
}

medicines = {
    mid: {
        "MedicineCategory": random.choice(["Antibiotic","Analgesic","Antipyretic","Antidepressant","Antihypertensive"]),
        "BrandName": fake.company(),
        "ChemicalContent": random.choice(["Paracetamol","Ibuprofen","Amoxicillin","Ciprofloxacin","Metformin"]),
        "ManufacturingDate": fake.date_between(start_date="-730d", end_date="-30d"),
        "ExpiryDate": fake.date_between(start_date="+30d", end_date="+1095d"),
        "Dosage": random.choice(["100mg","250mg","500mg","1g"])
    }
    for mid in medicine_ids
}

# Create fact_medications records
records = []
for _ in range(n_records):
    pid = random.choice(patient_ids)
    did = random.choice(doctor_ids)
    hid = random.choice(hospital_ids)
    phid = random.choice(pharmacy_ids)
    sid = random.choice(supplier_ids)
    mid = random.choice(medicine_ids)

    patient = patients[pid]
    doctor = doctors[did]
    hospital = hospitals[hid]
    pharmacy = pharmacies[phid]
    supplier = suppliers[sid]
    medicine = medicines[mid]

    price_per_unit = round(random.uniform(5, 500), 2)
    quantity = random.randint(1, 20)
    total_price = round(price_per_unit * quantity, 2)

    record = {
        "PatientID": pid,
        "DoctorID": did,
        "HospitalID": hid,
        "PharmacyID": phid,
        "SupplierID": sid,
        "MedicineID": mid,
        "Patient_Name": patient["Patient_Name"],
        "Patient_Gender": patient["Patient_Gender"],
        "Patient_Age": patient["Patient_Age"],
        "Patient_DOB": patient["Patient_DOB"],
        "Patient_Address": patient["Patient_Address"],
        "Patient_City": patient["Patient_City"],
        "Patient_State": patient["Patient_State"],
        "Patient_Country": patient["Patient_Country"],
        "Patient_PostalCode": patient["Patient_PostalCode"],
        "Patient_SatisfactionScore": patient["Patient_SatisfactionScore"],
        "Patient_BloodGroup": patient["Patient_BloodGroup"],
        "Patient_WaitTime": patient["Patient_WaitTime"],
        "DoctorFullName": doctor["DoctorFullName"],
        "Doctor_Specialization": doctor["Doctor_Specialization"],
        "Doctor_Years_Experience": doctor["Doctor_Years_Experience"],
        "Doctor_Email": doctor["Doctor_Email"],
        "Doctor_Shift_Type": doctor["Doctor_Shift_Type"],
        "Hospital_Name": hospital["Hospital_Name"],
        "Hospital_Provider_Name": hospital["Hospital_Provider_Name"],
        "Hospital_Type": hospital["Hospital_Type"],
        "Bed_Capacity": hospital["Bed_Capacity"],
        "Hospital_Address": hospital["Hospital_Address"],
        "Hospital_State": hospital["Hospital_State"],
        "Hospital_City": hospital["Hospital_City"],
        "Hospital_Country": hospital["Hospital_Country"],
        "Hospital_PostalCode": hospital["Hospital_PostalCode"],
        "PharmacyName": pharmacy["PharmacyName"],
        "PharmacyLocation": pharmacy["PharmacyLocation"],
        "PharmacyContact": pharmacy["PharmacyContact"],
        "SupplierName": supplier["SupplierName"],
        "ContactPerson": supplier["ContactPerson"],
        "SupplierContact": supplier["SupplierContact"],
        "SupplierEmail": supplier["SupplierEmail"],
        "SupplierAddress": supplier["SupplierAddress"],
        "SupplierCity": supplier["SupplierCity"],
        "SupplierState": supplier["SupplierState"],
        "SupplierPincode": supplier["SupplierPincode"],
        "ContractEndDate": supplier["ContractEndDate"],
        "MedicineCategory": medicine["MedicineCategory"],
        "BrandName": medicine["BrandName"],
        "ChemicalContent": medicine["ChemicalContent"],
        "ManufacturingDate": medicine["ManufacturingDate"],
        "ExpiryDate": medicine["ExpiryDate"],
        "Dosage": medicine["Dosage"],
        "PricePerUnit": price_per_unit,
        "Quantity": quantity,
        "TotalPrice": total_price
    }
    records.append(record)

# Save to CSV
df = pd.DataFrame(records)
df.to_csv("fact_medications.csv", index=False)

print("✅ fact_medications.csv with 500000 records generated successfully!")
