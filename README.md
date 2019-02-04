# utl-using-external-file-to-add-variable-label
Using external file to add variable label.
    Using external file to add variable labels                                                                  
                                                                                                                
    github                                                                                                      
    https://tinyurl.com/yboake99                                                                                
    https://github.com/rogerjdeangelis/utl-using-external-file-to-add-variable-label                            
                                                                                                                
    SAS forum                                                                                                   
    https://tinyurl.com/y9c2xxaw                                                                                
    https://communities.sas.com/t5/SAS-Programming/Using-external-file-to-add-variable-label/m-p/530423         
                                                                                                                
    Recent additions by Paul and Bart                                                                           
    ---------------------------------                                                                           
                                                                                                                
    Nice solution with base SAS and no macros                                                                   
                                                                                                                
    Paul Dorfman                                                                                                
    sashole@bellsouth.net                                                                                       
                                                                                                                
    proc sql noprint ;                                                                                          
      select catt (var, " label='", label, "'") into :modify separated by ", " from labeldata                   
      where var in (select name from dictionary.columns where libname="WORK" and memname="MYDATA")              
      ;                                                                                                         
      alter table mydata modify &modify ;                                                                       
    quit ;                                                                                                      
                                                                                                                
    Best regards,                                                                                               
    Paul Dorfman                                                                                                
                                                                                                                
                                                                                                                
    Another recent enhancement                                                                                  
                                                                                                                
    Sometimes dictionary queries can take a long time, EG server prior to 9.4M5?                                
                                                                                                                
    Bartosz Jablonski                                                                                           
    yabwon@gmail.com                                                                                            
                                                                                                                
    Skipping the dictionaries                                                                                   
                                                                                                                
    proc transpose data = mydata(obs=0) out = names(keep=_name_);                                               
    run;                                                                                                        
                                                                                                                
    proc sql noprint ;                                                                                          
      select catt (var, " label='", label, "'") into :modify separated by ", " from labeldata                   
      where var in (select _name_ from names)                                                                   
      ;                                                                                                         
      alter table mydata modify &modify ;                                                                       
    quit ;                                                                                                      
                                                                                                                
    Original Solution                                                                                           
                                                                                                                
    INPUT                                                                                                       
    =====                                                                                                       
                                                                                                                
    data labeldata;                                                                                             
      input var $ 1-3 labels $ 5-17;                                                                            
    cads4;                                                                                                      
    A01 Diebetes                                                                                                
    A02 Heart disease                                                                                           
    B01 Cancer                                                                                                  
    B02 Renal failure                                                                                           
    ;;;;                                                                                                        
    run;quit;                                                                                                   
                                                                                                                
    /*                                                                                                          
     WORK.LABELDATA total obs=4                                                                                 
                                                                                                                
      VAR    LABELS                                                                                             
                                                                                                                
      A01    Diebetes                                                                                           
      A02    Heart disease                                                                                      
      B01    Cancer                                                                                             
      B02    Renal failure                                                                                      
    */                                                                                                          
                                                                                                                
    data mydata;                                                                                                
     input ID A01 A02 B01 B02 ;                                                                                 
    cards4;                                                                                                     
    1 1 1 1 0                                                                                                   
    2 0 0 0 1                                                                                                   
    3 1 0 1 1                                                                                                   
    4 1 0 0 1                                                                                                   
    ;;;;                                                                                                        
    run;quit;                                                                                                   
                                                                                                                
    /*                                                                                                          
     WORK.MYDATA total obs=4                                                                                    
                                                                                                                
      ID    A01    A02    B01    B02                                                                            
                                                                                                                
       1     1      1      1      0                                                                             
       2     0      0      0      1                                                                             
       3     1      0      1      1                                                                             
       4     1      0      0      1                                                                             
    */                                                                                                          
                                                                                                                
                                                                                                                
    *          _       _   _                                                                                    
     ___  ___ | |_   _| |_(_) ___  _ __                                                                         
    / __|/ _ \| | | | | __| |/ _ \| '_ \                                                                        
    \__ \ (_) | | |_| | |_| | (_) | | | |                                                                       
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|                                                                       
                                                                                                                
    ;                                                                                                           
    %array(vars,data=labeldata,var=var);                                                                        
    %array(lbls,data=labeldata,var=labels);                                                                     
                                                                                                                
    proc datasets library = work memtype = data nolist;                                                         
         modify mydata;                                                                                         
         label                                                                                                  
           %do_over(vars lbls,phrase= ?vars = ?lbls );                                                          
         quit;                                                                                                  
    run;                                                                                                        
                                                                                                                
    /*                                                                                                          
             Variables in Creation Order                                                                        
                                                                                                                
    #    Variable    Type    Len    Label                                                                       
                                                                                                                
    1    ID          Num       8                                                                                
    2    A01         Num       8    Diebetes                                                                    
    3    A02         Num       8    Heart disease                                                               
    4    B01         Num       8    Cancer                                                                      
    5    B02         Num       8    Renal failure                                                               
    */                                                                                                          
                                                                                                                
