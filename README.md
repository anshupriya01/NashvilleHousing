
# Nashville Housing Data Cleaning Project

This repository demonstrates a comprehensive data cleaning process using SQL on a real estate dataset from Nashville. The goal of this project is to clean and prepare the data for further analysis and visualization by handling missing values, standardizing formats, and breaking down compound fields into usable components.

## Overview

The Nashville Housing dataset contains property records with various attributes including address, sale dates, and ownership details. To prepare this dataset for analysis, several transformations and cleaning steps were implemented using SQL.

## Key SQL Operations

### 1. Standardizing Date Format

```sql
ALTER TABLE NashvilleHousing
ADD SaleDateConverted DATE;

UPDATE NashvilleHousing
SET SaleDateConverted = CONVERT(DATE, SaleDate);
```

### 2. Populating Null Property Addresses

```sql
UPDATE a
SET PropertyAddress = ISNULL(a.PropertyAddress, b.PropertyAddress)
FROM Project..NashvilleHousing a
JOIN Project..NashvilleHousing b
    ON a.ParcelID = b.ParcelID
    AND a.[UniqueID ] <> b.[UniqueID ]
WHERE a.PropertyAddress IS NULL;
```

### 3. Splitting Address Components

**Property Address:**

```sql
ALTER TABLE NashvilleHousing
ADD SplitPropertyAddress NVARCHAR(255),
    SplitPropertyCity NVARCHAR(255);

UPDATE NashvilleHousing
SET SplitPropertyAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress)-1),
    SplitPropertyCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress)+1, LEN(PropertyAddress));
```

**Owner Address:**

```sql
ALTER TABLE NashvilleHousing
ADD SplitOwnerAddress NVARCHAR(255),
    SplitOwnerCity NVARCHAR(255),
    SplitOwnerState NVARCHAR(255);

UPDATE NashvilleHousing
SET SplitOwnerAddress = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 3),
    SplitOwnerCity = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 2),
    SplitOwnerState = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 1);
```

### 4. Standardizing Boolean Columns

```sql
SELECT SoldAsVacant,
       CASE 
           WHEN SoldAsVacant = 'Y' THEN 'Yes'
           WHEN SoldAsVacant = 'N' THEN 'No'
           ELSE SoldAsVacant
       END AS Standardized_SoldAsVacant
FROM Project..NashvilleHousing;
```

## Tools Used

- SQL Server
- Microsoft Excel (for dataset cleaning and preprocessing)
- Git & GitHub for version control

## Author

**Anshupriya Singh**  
[LinkedIn](https://www.linkedin.com/in/anshupriya-singh01) | [Portfolio](https://github.com/anshupriya01)

---

This project is part of Anshupriya's data portfolio showcasing skills in data cleaning, transformation, and SQL-based ETL operations.
