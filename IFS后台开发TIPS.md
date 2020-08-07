* 主键自增长
  ```
  key          LineId                  NUMBER               K-I-- {
     DbTableColumnDefaultValue "SAP_HIS_LINE_ID_SEQ.NEXTVAL";
  }
  ```
* 视图追加字段
  ```
  @Override
  VIEW Quotation_Line_Com IS
  SELECT Quotation_Order_API.Get_C_Sup_Quotation_No(ql.inquiry_no, ql.vendor_no, ql.revision_no)        c_sup_quotation_no,
         c_expiration_date              c_expiration_date,
         c_e_decision                   c_e_decision;
  ```
* 视图注释修改
  ```
  COLUMN Internal_Order IS
     FLAGS     = 'A-IU-'
     DATATYPE  = 'STRING(12)'
     PROMPT    = 'Internal Order';
  ```
* 视图样例
  ```
  -------------------- COMMON COLUMN DEFINITIONS ------------------------------
  --(+)190305 EnvPriyeN C_RPT-002-1(START)
  COLUMN Patter_Id IS
    Flags      = 'KMI-L'
    Datatype   = 'NUMBER'
    Prompt     = 'Pattern ID';
  COLUMN Patter_Name IS
    Flags      = 'AMIUL'
    Datatype   = 'STRING(50)'
    Prompt     = 'Pattern Name';
  --(+)190305 EnvPriyeN C_RPT-002-1(FINISH)
  -------------------- PUBLIC VIEW DEFINITIONS --------------------------------
  --(+)190305 EnvPriyeN C_RPT-002-1(START)
  VIEW C_Payment_Term_Lov IS
    Prompt     = 'Capex Payment Term'
    Table      = 'C_PAYMENT_TERM_TAB'
  SELECT DISTINCT  
        patter_id                      patter_id,
        patter_name                    patter_name
  FROM   c_payment_term_tab
  ORDER BY 1;
  --(+)190305 EnvPriyeN C_RPT-002-1(FINISH)
  -------------------- PRIVATE VIEW DEFINITIONS -------------------------------

  --(+)190305 EnvPriyeN C_RPT-002-1(START)
  @Override
  VIEW C_Payment_Term IS
    Prompt            = 'Capex Payment Term';
  --(+)190305 EnvPriyeN C_RPT-002-1(FINISH)

* 表字段默认值
  ```
  public       LoadDate                DATE                 A-IU- {
     DbTableColumnDefaultValue "sysdate";
  }
  ```
* 传给前台显示别名
  ```
  public       Amount                  NUMBER               A-IU- {
     LabelText "Amount in Document Currency";
  }
  ```
* 后台异常写法
  ```
  IF Purchase_Authorizer_API.Get_C_Rfq(company_, rec_.authorize_id) <> 'TRUE' THEN
     Error_SYS.Record_General(lu_name_, 'FWDAUTHLIMIT: :P1 has not enough authorization limit defined to authorize line no :P2', forwarded_to_, line_no_);
  END IF;
  ```
IFS API | 备注
---|---
Fnd_Session_API | 会话API，可以取得登陆人以及登陆程序语言等信息
Error_SYS | 异常API，常用语写后台报错
Client_SYS |常用语拼接attr值