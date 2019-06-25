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