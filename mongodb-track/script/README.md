## how to load js file into mongoshell ##

    #!/bin/bash
    homepath=/var/mongoScript/
    logpos=/var/assets/mongoshell.log
    mongoShellCMD='mongo localhost:27017/testDB -u user -p pass'
    #mongoShellCMD='mongo localhost:27017/testDB'
    rm $logpos
    echo 'run at '`date` >> $logpos
    #should do it in loop
    
    #warning error
    echo '********WARNING ERROR*********' >> $logpos
    $mongoShellCMD $homepath'account.js' 2>&1 >> $logpos


----------


    print("累计收益超过 280 的")
    print("user_id", "\t" ,"累计收益" );
    db.user_account.find(
        { "accumulated_earning":{ $gt: 280 }  }, 
        {"user_id":1 , "accumulated_earning":1 } ).forEach(function(result){
        print(result.user_id, "\t",result.accumulated_earning);
    }) 
    print('EOF\n');
