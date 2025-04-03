# Remittance Batch File [ANSI-835] Import Process

For ***Processing*** a Batch File, if we encounter any **ANSI-835** file, then they will call:

    Process835

## **EMCRECIEVERID** [*EMC Receiver ID*]

* Each Interface connection will have an ID, this is called as **EMC RECIEVER ID**.
* This is one of the main Indicator that we have received a *Remittance File*
     from the *Payer (Insurance Company.)*

## **Trigger Point for Import [1.0]**

* method called CreateFile, line: 1565 inside **Import.pm**
* After successfully working on a queue-message, that message will be deletd
    from the queue, so that it doesn't process the same message multiple times.

* Similarly, there will be an entry created in the simple queue table, so the consuming process will work on that queue if it is successfully worked, it will be removed from the simple queue table and it will be moved to simple queue work table.

## **Trigger Point for Import [2.0]**

* method ***CanBeRoutedToNewImport line 1463***, we checking if the file can be
    routed to the new import, as only less than 1%
    of the files are processed in the 1.0

* Queues are used similar to AWS Queues, if the Batch can
    be imported to the 2.0 systems then in the queue along
    with the Batch a Message will be posted.

### 1.0 System

* Databases are used as Queues, known as Queue Schema. We have a *DB Table*:

  * SIMPLE_QUEUE

* We put an Entry in the Table which will be processed by the **Consumer**, if its successful it will be removed from *SIMPLE_QUEUE* and will be moved to *SIMPLE_QUEUE_WORK*

* An **Engine** object will be created and using this Engine Object all the methods will be called.

* These Imports will be caleed from **import.pm**

* There are also *Overrides* for this Engine class if any Payer has different Business Logic. All the engine override classes can be found in the **Engine Folder**.

#### method **createFile**

* **First** we will first check the *ANSI-version*
  * we are doing the validations and identifying the **ANSI-version** and the **Format**.
  * inside **ANSI-835** we are supporting 2 formats:
    * 4010
    * 5010 *mostly remittances will come in this format*

    these are standards and will be undated
* **Next** we call thr ***readFile*** method
  * in this method we read the file
  * then we call the method to *parse* it:
    * **ParseANSIMessage** at *line 126* 
* **Next** we parse it:

#### Parsing

* **First** we parse it in the file **Parse.pm** l
* *line: **47***
  
  its inside  Interface/Format/ANSI/Parse.pm
* we check the *spec* here
* Here all the line-items are converted into segments
* **Next** we create a *canonical-model* out of it
  * *canonical-model* is a Data Structure, similar to objects in JavaScript
  * method ***MapToCanonical*** at line *132*
* After receiving a *Remittance* file:
  * we convert it into a Data Structure in the below way:

    * ERAFILE => Entry for the File that we received
      * ERABATCH => All the BPR within that particular file
        * ERARECORDS => CLAIMS & CHARGES  are represented together here

    There is no Isolated way to Identify the Claim and Charges
    For Each Charge, we create a separate ERARECORD
  * Even **Batch Exceptions** are represented as *ERA-RECORDS*
  * We have a Column in ERARECORD as **Action**
    * Here we mention the *Action* as **BATCHEXCEPTION**
      * method **map835** does the same
      * There will be a **HashMap** created with *key*: *batch*
      * value of *batch* will have another *HashMap* with separate Fields as Keys and
      Value Pairs
    * **Kick**  can be at **Claim** level or **Charge** level
      * **Kick Transformations** are also done in this part
      * some *kick transformations* may also override depending on the Business Loginge required

        here we will transform the kick information for the athena purpose
      * Thereare multiple transformation rules
  * line *151* we are converting all the Claim level fields into **ERA Records** [error records]
* before submitting to a *Payer* there aare a lot of processes, like ***Claim Scrapping*** wehere Claims are checked if they are Fradulent Claims
