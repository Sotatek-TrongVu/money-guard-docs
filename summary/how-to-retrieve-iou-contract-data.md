# Guide: How to Retrieve IOU Contract Data

This guide will help you access the contract at [BscScan Testnet](https://testnet.bscscan.com) and retrieve IOU data using the `getIOUAtIndex` and `getIOUDataCount` functions.

---

## 1. Open the Contract on BscScan
In this example, the IOU contract address is: 0xbdb1165f934f2a6776fec47c0599685f3689b93d

- Go to: [https://testnet.bscscan.com/address/0xbdb1165f934f2a6776fec47c0599685f3689b93d#readContract](https://testnet.bscscan.com/address/0xbdb1165f934f2a6776fec47c0599685f3689b93d#readContract)
- Click the **"Contract"** tab, then the **"Read Contract"** sub-tab.

---

## 2. Check How Many Data Slots Are Used

- Find the function named **`getIOUDataCount`**.
- Click to expand it, then click **"Query"**.
- The result will show you the total number of data slots used for the IOU.  
  - For example, if it returns `5`, there are 5 slots (from index 0 to 4).

---

## 3. Retrieve Data by Index

- Find the function named **`getIOUAtIndex`**.
- Enter the index number you want to query:
  - **Index 0**: This always stores the **PDF hash**.
  - **Index 1 and above**: These store other IOU-related data (such as status, timestamps, etc.).
- Click **"Query"** to get the value at that index.

**Repeat** for each index from `0` up to `getIOUDataCount - 1` to retrieve all stored data.

---

## 4. Example

Suppose `getIOUDataCount` returns `3`:
- Query `getIOUAtIndex(0)` → PDF hash (e.g., `Qm...`)
- Query `getIOUAtIndex(1)` → First data field (e.g., status)
- Query `getIOUAtIndex(2)` → Second data field (e.g., timestamp)

---

**Summary Table:**

| Index | Data Stored         |
|-------|---------------------|
| 0     | PDF hash            |
| 1     | Other data (field 1)|
| 2     | Other data (field 2)|
| ...   | ...                 |

---

