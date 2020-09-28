## 事件列表
Action Type        |    Action Description    |     Comments
-----        |------| -------
PM_DataItemEntered| 进入控件 | 当数据源想要在移动控件时执行某些操作（例如启用/禁用按钮）时，此消息非常有用。
SAM_SetFocus      |    获得焦点| 
PM_LookupInit | 下拉列表初始化 | PM_LookupInit消息在用户第一次下拉列表时发送。 填充列表后，不再发送PM_LookupInit。 应用程序可以调用LookupInvalidateData函数将当前列表数据标记为无效，并在下次列表下拉时再次发送PM_LookupInit。
PM_DataItemPopulate | 数据加载 | 在使用服务器的值填充数据源之后，将Const.PM_DataItemPopulate消息发送到数据源中的所有数据项。
PM_DataItemValidate | 数据验证 | 该框架能够自动执行多种类型的验证。 应用程序只需捕获PM_DataItemValidate即可执行其他验证。 该框架将自动执行以下验证：</BR>必填字段经验证具有值</BR>调用Foundation1属性中指定的任何验证方法
Sys.SAM_KillFocus | 失去焦点 | 事件，可用于自动填充需要调用函数取值的场合
Sys.SAM_AnyEdit | 数据修改 | 事件
Sal.TblAnyRows  | 确定任何行是否与某些标志匹配 | 常用语判断是否有行选中
vrtDataSourceSaveModified  | 修改保存事件（包括子新增） | 例子中在Base方法调用前增加自己的逻辑判断
vrtDataRecordExecuteModify | 仅修改保存事件 | 与vrtDataSourceSaveModified不同在于仅修改出发，新增不会触发
PM_DataItemQueryEnabled    | 前台控制某字段是否可用 | 例如根据另一个字段状态控制checkbox是否可以check
vrtActivate | 画面激活 | 新打开算激活，画面切换不走该方法，可用于修改标题，打开画面增加逻辑，见例
DataSourceUserWhere | 拼接一个用户及的where条件 | 用于增加条件或者打开画面加载限定条件的数据，见例
OnRearrangeMergedMenuItems | 重新排列菜单项 | LAA中客户化的右键菜单无法调整位置，使用该方法进行调整
cbNSendFlag_WindowActions | CheckBox只读 | 对于只读的CheckBox和ComboBox必须进行控制，不然新增数据的时候可以修改值，也可以用PM_DataItemQueryEnabled
PM_DataItemQueryEnabled | 控制控件是否可编辑 | 
DataSourceSaveMarkCommitted() | 标记已提交 | 用于欺骗程序修改已经提交。可用于控制保存按钮不可用的情况下，检索数据提示未保存问题。
dfsCustomerPoNo.EditDataItemStateGet() == Ifs.Fnd.ApplicationForms.Const.EDIT_Changed |前台判断值变更|
this.DataRecordStateGet() == "Planned" | 如果画面支持状态机，则可以取得objstate |
FromHandle(i_hWndParent) | 取亲级画面 | frmCOrderChgQuotation_Cust.FromHandle(i_hWndParent).cbUsePriceInclTax.Checked |
PM_DataItemLovUserWhere | 处理LOV传入条件 | 用于复杂LOV，条件无法通过设置传入 |
PM_DataItemLovDone | LOV选择后处理 | 对字段赋值 |
DbTransactionBegin;DbTransactionEnd | 前台事务写法 | 保持事务一致性 |
DataSourceRefresh | 画面刷新 | 发送消息或者方法调用都可以。 DataSourceRefresh(Ifs.Fnd.ApplicationForms.Const.METHOD_Execute); |
DataSourceIsDirty | 判断数据是否修改 | 可用于局部控制某页签禁止修改等 |


## 获得焦点
```C#
        case Sys.SAM_SetFocus:
            this.dfsComponent_OnSAM_SetFocus(sender, e);
            break;
```
## 画面激活，设置标题
```C#
        public override SalNumber vrtActivate(fcURL URL)
        {
            #region Actions
            using (new SalContext(this))
            {
                if (Ifs.Fnd.ApplicationForms.Var.DataTransfer.RecCountGet() > 0)
                {
                    InitFromTransferredData();
                    Ifs.Fnd.ApplicationForms.Var.DataTransfer.Reset();
                    Sal.SendMsg(cmbName, Sys.SAM_Click, 0, 0);
                    this.cbValidRates.Checked = false;
                    return false;
                }
                SetWindowTitle();
                return base.Activate(URL);
            }
            #endregion

        }
```
## 回车或者其他键盘按键
```C#
        private void fndTextBoxFind_KeyDown(object sender, System.Windows.Forms.KeyEventArgs e)
        {
            if( e.KeyCode == Keys.Enter && !Sal.IsNull(fndTextBoxFind))
            {
                FindActivity();
            }
        }
```
## 下拉列表初始化
```C#
        case Ifs.Fnd.ApplicationForms.Const.PM_LookupInit:
            this.cmbLedger_OnPM_LookupInit(sender, e);
            break;
```
```C#
        private void cmbLedger_OnPM_LookupInit(object sender, WindowActionsEventArgs e)
        {
            #region Actions
            e.Handled = true;
            if (Sal.SendClassMessage(Ifs.Fnd.ApplicationForms.Const.PM_LookupInit, Sys.wParam, Sys.lParam)) 
            {
                Sal.ListDelete(this.cmbLedger.i_hWndSelf, 3);
                Sal.ListDelete(this.cmbLedger.i_hWndSelf, 3);
                e.Return = true;
                return;
            }
            e.Return = false;
            return;
            #endregion
        }
```
## 数据加载事件
```C#
        case Ifs.Fnd.ApplicationForms.Const.PM_DataItemPopulate:
            this.cmbAccntType_OnPM_DataItemPopulate(sender, e);
            break;
```
```C#
        private void cmbAccntType_OnPM_DataItemPopulate(object sender, WindowActionsEventArgs e)
        {
            #region Actions
            e.Handled = true;
            this.nTempFlag = 0;
            this.CheckConnect();
            e.Return = Sal.SendClassMessage(Ifs.Fnd.ApplicationForms.Const.PM_DataItemPopulate, Sys.wParam, Sys.lParam);
            return;
            #endregion
        }
```
## 数据验证
```C#
        case Ifs.Fnd.ApplicationForms.Const.PM_DataItemValidate:
            this.dfsViewName1_OnPM_DataItemValidate(sender, e);
            break;
```
```C#
        private void dfFromFileType_OnSAM_Validate(object sender, WindowActionsEventArgs e)
        {
            #region Actions
            e.Handled = true;
            using(SignatureHints hints = SignatureHints.NewContext())
            {
                hints.Add("Ext_File_Type_API.Get_Description", System.Data.ParameterDirection.Input);
                if (!(this.dfFromFileType.DbPLSQLBlock(cSessionManager.c_hSql, ":i_hWndFrame.dlgExtFileCopyFileType.dfsDescriptionFrom :=  " + cSessionManager.c_sDbPrefix + "Ext_File_Type_API.Get_Description(\r\n" +
                    ":i_hWndFrame.dlgExtFileCopyFileType.dfFromFileType)"))) 
                {
                    e.Return = Sys.VALIDATE_Cancel;
                    return;
                }
            }
            e.Return = Sys.VALIDATE_Ok;
            return;
            #endregion
        }
```
## 显示后台代码返回的info_信息
```sql
        Clint_Sys.Add_Info(lu_name_, 'SuccMsg: This is info test, msg is :P1', 'hello world!');
        info_ := Clint_Sys.Get_All_Info();
```
```C#
        private void cmdTestArchive_Execute(object sender, Fnd.Windows.Forms.FndCommandExecuteEventArgs e)
        {
            #region Action
            using (new SalContext(this))
            {
                lsInfo = Ifs.Fnd.ApplicationForms.Const.strNULL;
                Sal.WaitCursor(true);
                if (DbPLSQLBlock(cSessionManager.c_hSql, @"&AO.Media_Archive_API.Test_Archive(
                                                                    :i_hWndFrame.tbwMediaArchives.lsInfo        OUT,
                                                                    :i_hWndFrame.tbwMediaArchives.colnArchiveNo IN);"))
                {
                    HandleSqlResult(tbwMediaArchives.FromHandle(i_hWndFrame).lsInfo);
                    Sal.WaitCursor(false);
                }
            }
            #endregion
        }
```
## 判定右键选择单行数据可用
```C#
        private void menuTbwMethods_ShowSupplier_Inquire(object sender, FndCommandInquireEventArgs e)
        {
            SalNumber nCurrentRow = 0;
            SalNumber nCount = 0;
            // only one row selected
            nCount = 0;
            nCurrentRow = Sys.TBL_MinRow;
            while (Sal.TblFindNextRow(i_hWndSelf, ref nCurrentRow, Sys.ROW_Selected, 0))
            {
                nCount = nCount + 1;
                if (nCount > 1)
                {
                    break;
                }
            }
            if (nCount != 1)
            {
                ((FndCommand)sender).Enabled = false;
                return;
            }
            else
            {
                ((FndCommand)sender).Enabled = true;
                return;
            }
        }
```
## 判断遇到某属性为指定状态时数据更新报错（前台）
```C#
        public override SalBoolean vrtDataSourceSaveModified()
        {
            if (this.cbPrinted.Checked)
            {
                Ifs.Fnd.ApplicationForms.Int.AlertBox(Properties.Resources.TEXT_ERROR01, Ifs.Fnd.ApplicationForms.Properties.Resources.CAPTION_Error, Ifs.Fnd.ApplicationForms.Const.CRITICAL_Ok);
                return false;
            }
            return base.vrtDataSourceSaveModified();
        }
```
## 画面激活，自动加载承认时间为空的数据
**该代码有问题，建议使用IFS功能设置为打开加载指定条件数据**
```C#
        public override SalNumber vrtActivate(fcURL URL)
        {
            using (new SalContext(this))
            {
                if (Ifs.Fnd.ApplicationForms.Var.DataTransfer.RecCountGet() == 0)
                {
                    DataSourceUserWhere(Ifs.Fnd.ApplicationForms.Const.METHOD_Execute, ((SalString)"n_applied_date IS NULL").ToHandle());//左侧不需要写where和and
                    Sal.SendClassMessage(Ifs.Fnd.ApplicationForms.Const.PM_DataSourcePopulate, Ifs.Fnd.ApplicationForms.Const.METHOD_Execute, Ifs.Fnd.ApplicationForms.Const.POPULATE_Single);
                    return false;
                }
            }
            Sal.WaitCursor(false);
            return base.vrtActivate(URL);
        }
```
## 右键可用性检查（数据行选择、是否有更新未保存）
```C#
        private void menuTbwMethods_menu_Authorize_Acknowledge_Inquire(object sender, Fnd.Windows.Forms.FndCommandInquireEventArgs e)
        {
            //权限判断
            if (!(Ifs.Fnd.ApplicationForms.Var.Security.IsMethodAvailable("Order_Quotation_API.C_Approval") && Ifs.Fnd.ApplicationForms.Var.Security.IsPresObjectAvailable(Pal.GetActiveInstanceName("frmSupplierPerPart"))))
            {
                ((FndCommand)sender).Enabled = false;
                return;
            }
            // 数据未保存判断
            if (Sal.SendMsg(this, Ifs.Fnd.ApplicationForms.Const.PM_DataSourceSave, Ifs.Fnd.ApplicationForms.Const.METHOD_Inquire, 0))
            {
                ((FndCommand)sender).Enabled = false;
                return;
            }
            // 行选中判断
            if (!(Sal.TblAnyRows(this, Sys.ROW_Selected, 0)))
            {
                ((FndCommand)sender).Enabled = false;
                return;
            }

            ((FndCommand)sender).Enabled = checkApproveInquire();
            return;
        }
```
## 重新排列菜单项
```C#
        protected override void OnRearrangeMergedMenuItems(FndContextMenuStrip contextMenu)
        {
            base.OnRearrangeMergedMenuItems(contextMenu);

            // Move the menu item "menuItem_Fetch_Authorization_Rule" defined in menu "menuFrmMethods_Cust"
            // to the location just befor the menu item "menuItem_Release" which is defined in menu "menuFrmMethods".
            contextMenu.MoveItemBefore(this.menuItem_Fetch_Authorization_Rule, this.menuItem_Release);
        }
```
## CheckBox按钮不可编辑
```C#
        private void cbNSendFlag_WindowActions(object sender, WindowActionsEventArgs e)
        {
            switch (e.ActionType)
            {
                case Sys.SAM_Click:
                    e.Handled = true;
                    this.cbNSendFlag.Checked = !(this.cbNSendFlag.Checked);
                    break;
            }
        }
```
## 下拉列表控件不可编辑
```C#
        private void cmbNAppStatus_WindowActions(object sender, WindowActionsEventArgs e)
        {
            switch (e.ActionType)
            {
                case Ifs.Fnd.ApplicationForms.Const.PM_DataItemQueryEnabled:
                    e.Return = Ifs.Fnd.ApplicationForms.Const.EDITSTATEFieldNotEnabled;
                    break;
            }
        }
```
## ZOOM画面跳转功能代码
```C#
        private void dfsCTechnologyTypeNo_OnPM_DataItemZoom(object sender, WindowActionsEventArgs e)
        {
            e.Handled = true;
            if (Sys.wParam == Ifs.Fnd.ApplicationForms.Const.METHOD_Inquire)
            {
                e.Return = Ifs.Fnd.ApplicationForms.Var.Component.IsWindowAvailable(Pal.GetActiveInstanceName("frmCTechnologyType_Cust")) && Ifs.Fnd.ApplicationForms.Var.Security.IsDataSourceAvailable(Pal.GetActiveInstanceName("frmCTechnologyType_Cust"));
                return;
            }

            if (Sys.wParam == Ifs.Fnd.ApplicationForms.Const.METHOD_Execute)
            {
                this.sItemNames[0] = "C_TECHNOLOGY_TYPE1";
                this.hWndItems[0] = this.dfsCTechnologyType;
                Ifs.Fnd.ApplicationForms.Var.DataTransfer.Init(this.p_sLogicalUnit, this, this.sItemNames, this.hWndItems);
                SessionNavigate(Pal.GetActiveInstanceName("frmCTechnologyType_Cust"));
                e.Return = true;
                return;
            }

            e.Return = Sal.SendClassMessage(Ifs.Fnd.ApplicationForms.Const.PM_DataItemZoom, Sys.wParam, Sys.lParam);
        }
```
## LOV传入条件
```C#
        private void colsCDepartmentId_OnPM_DataItemLovUserWhere(object sender, WindowActionsEventArgs e)
        {
            #region Actions
            e.Handled = true;
            e.Return = ((SalString)"COMPANY = :i_hWndFrame.dlgCOrderApprovalDept_Cust.sCompany").ToHandle();
            return;
            #endregion
        }
```
## LOV选择数据后赋值
```C#
        private void colsCDepartmentId_OnPM_DataItemLovDone(object sender, WindowActionsEventArgs e)
        {
            #region Actions
            e.Handled = true;
            Sal.SendMsg(this.tblCOrderApprovalDept_colsCDepartmentId, Ifs.Fnd.ApplicationForms.Const.PM_DataItemValueSet, 1, Vis.StrSubstitute(Ifs.Fnd.ApplicationForms.Int.PalAttrGet("DEPT_ID", SalString.FromHandle(Sys.lParam)), ((SalNumber)31).ToCharacter(), "").ToHandle());
            Sal.SendMsg(this.tblCOrderApprovalDept_colsCDepartmentDesc, Ifs.Fnd.ApplicationForms.Const.PM_DataItemValueSet, 1, Vis.StrSubstitute(Ifs.Fnd.ApplicationForms.Int.PalAttrGet("DEPT_NAME", SalString.FromHandle(Sys.lParam)), ((SalNumber)31).ToCharacter(), "").ToHandle());
            #endregion
        }
```
## 头为下达状态则（页签1）修改报错，重写页签1的vrtDataSourceSaveModified
```
    public override SalBoolean vrtDataSourceSaveModified()
    {
        if (frmOrderQuotation_Cust.FromHandle(i_hWndParent).DataRecordStateGet() == "Released")
        {
            if (this.DataSourceIsDirty(Ifs.Fnd.ApplicationForms.Const.METHOD_Inquire))
            {
                Ifs.Fnd.ApplicationForms.Int.AlertBox(Properties.Resources.CC_E00008, Ifs.Fnd.ApplicationForms.Properties.Resources.CAPTION_Error, Ifs.Fnd.ApplicationForms.Const.CRITICAL_Ok);
                return false;
            }
        }
    }
```