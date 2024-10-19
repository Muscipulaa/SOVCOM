# SOVCOM
We have dataset, which contain information about bank's clients, with titles:  

**TIME** - date and time of the transaction  
**TRANSACTION_ID**	- unique identifier of operation  
**AMOUNT** - sum of operations  
**CLIENT_ID**	- unique identifier of each client  
**BANK_ID** - unique identifiers of banks  
**CITY** - city of the transactions  
**OPERATION_TYPE** - type of operation   
**FRAUD_FLAG** - sign of fraud< if it's 1 - fraud, 0 - classic operation.  

The first, we must to calculate nominal turnover, turnover dynamics and percentage growth for understanding the popular and large banks. It's can help us to make more progressive marketing actions for every bank.    

So, we have new titles:  
**NOMINAL_TRANSACT** - nominal turnover  
**COUNT_TRANSACT** - number of transactions  
**DYNAMIC**	- dynamics of turnover  
**PERCENTAGE_GROWTH** - percentage of growth  

Secondly, we must to analyze the suspestful operations. Contain information about fraud's cities and clients. Pass the information to the security department.

Conclusions:
1) The leader in each month is the bank with ID==1, demonstrating the highest values ​​both in nominal turnover (NOMINAL_TRANSACT) and in the number of transactions (COUNT_TRANSACT). This indicates high activity of the bank's clients and efficient use, providing it with a significant competitive advantage.
2) Most fraudulent withdrawals through partner banks are committed from 8 am, with a peak at 11 am, and noticeable values ​​are also observed at 12, 5 and 9 pm. Presumably, fraudsters prefer to work during working hours, when clients are more active, so that their actions are less noticeable. Evening hours of fraudulent activities can be explained by the reduced attention of clients.
3) **Withdrawals via partner ATMs** have the highest number of fraudulent transactions and also the highest share of fraudulent transactions (1.07%). This may indicate higher fraudulent activity in this segment, possibly due to easier access to user information.
**Deposits via partner ATMs** also show a significant number of fraudulent transactions, although its share (0.41%) is significantly lower than that of withdrawals;
**Withdrawals via other ATMs** have the lowest number of fraudulent transactions (12,445) and an extremely low share of fraudulent transactions (0.0013%), which may indicate that fraudsters avoid using other ATMsl;
4) The peaks in fraudulent transactions coincide with peaks in regular transactions, which may indicate two possible explanations: either there is an error in classifying the transaction as fraudulent, or the fraudsters deliberately choose a time of high customer activity so that their transactions can be more easily disguised among regular transactions.
In addition, fraudsters prefer to use the ATMs of transaction partners, since such actions may be less noticeable.
5)High nominal turnover and number of transactions indicate strong user activity, which is a positive sign for the bank.
The spread in transaction amounts indicates the presence of both standard and large transactions.
The almost zero percentage growth indicates stability, but the presence of sharp fluctuations indicates the need for further analysis of the factors affecting customer activity


