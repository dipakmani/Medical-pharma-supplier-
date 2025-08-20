import pandas as pd
import random
import faker
from datetime import datetime, timedelta

# Initialize Faker
fake = faker.Faker()

# Record size
n = 500000  

# --------------------------
# Generate unique master data
# --------------------------
num_patients = 10000
num_doctors = 500
num_hospitals = 50
num_departments = 20
num_pharmacies = 200
num_suppliers = 800
num_medicines = 2000   # Unique medicines master data

# Patients
patients = [
    {
        "PatientID": i,
        "PatientName": fake.name(),
        "PatientGender": random.choices(["Male", "Female"], weights=[0.6, 0.4])[0],  # 60% Male, 40% Female
        "PatientAge": random.randint(1, 90),
        "PatientAddress": fake.address().replace("\n", ", ")
    }
    for i in range(1, num_patients + 1)
]

# Doctors
doctors = [
    {
        "DoctorID": i,
        "DoctorName": fake.name(),
        "Specialization": random.choice(["Cardiology", "Neurology", "Orthopedics", "Oncology", "Pediatrics", "General Medicine"]),
        "ExperienceYears": random.randint(1, 40)
    }
    for i in range(1, num_doctors + 1)
]

# Hospitals
hospitals = [
    {
        "HospitalID": i,
        "HospitalName": fake.company() + " Hospital",
        "City": fake.city(),
        "State": fake.state(),
        "Country": fake.country()
    }
    for i in range(1, num_hospitals + 1)
]

# Departments
departments = [
    {
        "DepartmentID": i,
        "DepartmentName": random.choice(["Surgery", "ICU", "Emergency", "Pharmacy", "Radiology", "Cardiology"])
    }
    for i in range(1, num_departments + 1)
]

# Pharmacies
pharmacies = [
    {
        "PharmacyID": i,
        "PharmacyName": fake.company() + " Pharmacy",
        "Location": fake.city(),
        "ContactNumber": fake.phone_number()
    }
    for i in range(1, num_pharmacies + 1)
]

# Suppliers
suppliers = [
    {
        "SupplierID": i,
        "SupplierName": fake.company(),
        "ContactPerson": fake.name(),
        "ContactNumber": fake.phone_number(),
        "Email": fake.email(),
        "Address": fake.address().replace("\n", ", "),
        "City": fake.city(),
        "State": fake.state(),
        "Pincode": fake.zipcode(),
        "ContractEndDate": fake.date_between(start_date="+1y", end_date="+10y")
    }
    for i in range(1, num_suppliers + 1)
]

# Medicines (Master Data)
medicine_categories = ["Antibiotic", "Analgesic", "Antidepressant", "Antihypertensive", "Antidiabetic"]
brand_names = ["MediLife", "HealthPlus", "CureFast", "WellCare", "PharmaPro"]
chemical_contents = ["Paracetamol", "Ibuprofen", "Metformin", "Amoxicillin", "Atorvastatin"]

medicines = [
    {
        "MedicineID": i,
        "MedicineCategory": random.choice(medicine_categories),
        "BrandName": random.choice(brand_names),
        "ChemicalContent": random.choice(chemical_contents)
    }
    for i in range(1, num_medicines + 1)
]

# --------------------------
# Generate Fact_Medications
# --------------------------
fact_records = []
for i in range(1, n + 1):
    patient = random.choice(patients)
    doctor = random.choice(doctors)
    hospital = random.choice(hospitals)
    department = random.choice(departments)
    pharmacy = random.choice(pharmacies)
    supplier = random.choice(suppliers)
    medicine = random.choice(medicines)
    
    mfg_date = fake.date_between(start_date="-3y", end_date="-6m")
    expiry_date = mfg_date + timedelta(days=random.randint(180, 1095))  # 6 months to 3 years later
    
    record = {
        # IDs
        "PatientID": patient["PatientID"],
        "DoctorID": doctor["DoctorID"],
        "HospitalID": hospital["HospitalID"],
        "DepartmentID": department["DepartmentID"],
        "PharmacyID": pharmacy["PharmacyID"],
        "SupplierID": supplier["SupplierID"],
        "MedicineID": medicine["MedicineID"],
        
        # Patient attributes
        "PatientName": patient["PatientName"],
        "PatientGender": patient["PatientGender"],
        "PatientAge": patient["PatientAge"],
        "PatientAddress": patient["PatientAddress"],
        
        # Doctor attributes
        "DoctorName": doctor["DoctorName"],
        "Specialization": doctor["Specialization"],
        "ExperienceYears": doctor["ExperienceYears"],
        
        # Hospital attributes
        "HospitalName": hospital["HospitalName"],
        "HospitalCity": hospital["City"],
        "HospitalState": hospital["State"],
        "HospitalCountry": hospital["Country"],
        
        # Department attributes
        "DepartmentName": department["DepartmentName"],
        
        # Pharmacy attributes
        "PharmacyName": pharmacy["PharmacyName"],
        "PharmacyLocation": pharmacy["Location"],
        "PharmacyContact": pharmacy["ContactNumber"],
        
        # Supplier attributes
        "SupplierName": supplier["SupplierName"],
        "ContactPerson": supplier["ContactPerson"],
        "SupplierContact": supplier["ContactNumber"],
        "SupplierEmail": supplier["Email"],
        "SupplierAddress": supplier["Address"],
        "SupplierCity": supplier["City"],
        "SupplierState": supplier["State"],
        "SupplierPincode": supplier["Pincode"],
        "ContractEndDate": supplier["ContractEndDate"],
        
        # Medicine attributes
        "MedicineCategory": medicine["MedicineCategory"],
        "BrandName": medicine["BrandName"],
        "ChemicalContent": medicine["ChemicalContent"],
        "ManufacturingDate": mfg_date,
        "ExpiryDate": expiry_date,
        "Dosage": str(random.randint(1, 2)) + " tablet(s) " + random.choice(["daily", "twice a day", "weekly"]),
        "Quantity": random.randint(1, 30)
    }
    
    fact_records.append(record)

# Convert to DataFrame
df = pd.DataFrame(fact_records)

# Save to CSV
df.to_csv("fact_medications.csv", index=False)

print("✅ fact_medications.csv generated with", n, "records including MedicineID")
