# Linux_shell_record
shell 陣列

# 陣列

- 範例 1 : 重複執行同一件事
    
    ```bash
    #設定陣列項目
    #陣列長度判斷 ${#arr[@]}
    arr_01=("cc" "cpu_lim_use" "cpu_lim_idle" "cpu_lim_load")
    
    i=1
    j=0
    while [ $i -le ${#arr[@]} ] 
    do
    
       new=f
       #等於某一個值
       set=${arr_01[$j]}
       #設定為特定值
       set_01=${arr[$i]}
       list=`cat tmon.conf | grep $set_01 `
       old=`cat tmon.conf | grep -w $set | grep -v '#' | awk -F \" '{print $2}'`
       echo " "
       echo "$i." $list
       echo " 原設定 : " $old
       read new
    
       if [ "$new" == "" ] ; then
          new=$old
       else
          sed -i -e "s/$set=\"$old\"/$set=\"$new\"/" tmon.conf
       fi
    
       new=`cat tmon.conf | grep -w $set | grep -v '#' | awk -F \" '{print $2}'`
       echo " 新設定 : " $new
       i=$(($i+1))
       j=$(($j+1))
    
    done
    
    ```
    
- 範例 2 : 檔案內容相似 ， 只有部分需調整
    
    ```bash
    
    a=(topprd toptst toppth topstd topprd-ws)
    
    i=0
    n=5
    
    #陣列長度判讀 ${#a[@]}
    for ((i=0;i < ${#a[@]};i++))
    do
       echo ${a[$i]}
       b=${a[$i]}
    
    #檔案內容寫入
    cat << EOF > gas-$b.service
    #20220309 v3.1.02 add ( for linux kernel ) 
    #                     LimitSTACK=infinity
    #                     LimitNOFILE=infinity
    #                     TasksMax=2000000 ( memory not enough )
    
    [Unit]
    Description=Start Fastcgidispatch...
    
    [Service]
    EOF
    
    #需調整部分調整後再寫入
    cat << EOF >> gas-$b.service
    ExecStart=/u1/genero/gas/bin/fastcgidispatch -s -f /u1/genero/as-$b.xcf
    User=tiptop
    Group=tiptop
    Restart=always
    #LimitNOFILE=130000  #20220309 mark
    #202220309 add (s)
    LimitNOFILE=infinity
    LimitSTACK=infinity
    TasksMax=2000000
    #20220309 add (e)
    
    [Install]
    WantedBy=multi-user.target
    EOF
    
    chmod 664 gas-$b.service
    
    done
    ```
    
- 範例 3 : 撈取字串轉存 陣列
    
    ```bash
    
    IFS=':'
    
    set -A array `env | grep CLASSPATH`
    echo ${array[0]}
    
    echo '' > 123.txt
    
    for index in "${!array[@]}"
    do
        echo "$index ${array[index]}"
        if [ "${array[index]}" != "" ] ; then
           ls -l ${array[index]} >> 123.txt
        fi
    done
    ```
