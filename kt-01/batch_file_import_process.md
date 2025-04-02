# Remittance Batch File [ANSI-835] Import Process
For ***Processing*** a Batch File, if we encounter any **ANSI-835** file, then they will call:

    Process835

⭐**EMCRECIEVERID**: 
    
    Each Interface connection will have an ID, this is called as **EMC RECIEVER ID**.
    
    This is one of the main Indicator that we have received a *Remittance File*
     from the *Payer (Insurance Company.)*

⭐ **Trigger Point for Import [1.0]:**
* method called ***CreateFile***, line: 1565 inside ***Import.pm***
* After successfully working on a queue-message, that message will be deletd
    from the queue, so that it doesn't process the same message multiple times.

* Similarly, there will be an entry created in the simple queue table, so the consuming process will work on that queue if it is successfully worked, it will be removed from the simple queue table and it will be moved to simple queue work table.

⭐ **Trigger Point for Import [2.0]:**
* method ***CanBeRoutedToNewImport line 1463***, we checking if the file can be 
    routed to the new import, as only less than 1%
    of the files are processed in the 1.0

* Queues are used similar to AWS Queues, if the Batch can
    be imported to the 2.0 systems then in the queue along
    with the Batch a Message will be posted.
    

## 1.0 System

* Databases are used as Queues, known as Queue Schema. We have a *DB Table*:

    + SIMPLE_QUEUE

* We put an Entry in the Table which will be processed by the **Consumer**, if its successful it will be removed from *SIMPLE_QUEUE* and will be moved to *SIMPLE_QUEUE_WORK*

* An **Engine** object will be created and using this Engine Object all the methods will be called.

* These Imports will be caleed from **import.pm**

* Thre are also *Overrides* for this Engine class if any Payer has different Business Logic. All the engine override classes can be found in the **Engine Folder**.



    
