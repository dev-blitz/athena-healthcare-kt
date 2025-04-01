# Remittance Batch File [ANSI-835] Import Process
For ***Processing*** a Batch File, if we encounter any **ANSI-835** file, then they will call:

    Process835

⭐**EMCRECIEVERID**: 
    
    Each Interface connection will have an ID, this is called as **EMC RECIEVER ID**.
    
    This is one of the main Indicator that we have received a *Remittance File*
     from the *Payer (Insurance Company.)*

⭐ **Trigger Point for Import [1.0]:**

    method called CreateFile, line: 1565


⭐ **Trigger Point for Import [2.0]:**

    Queues are used similar to AWS Queues, if the Batch can
    be imported to the 2.0 systems then in the queue along
    with the Batch a Message will be posted.
    
    method CanBeRoutedToNewImport line 1463, we checking if the file can be 
    routed to the new import, as only less than 1%
    of the files are processed in the 1.0
