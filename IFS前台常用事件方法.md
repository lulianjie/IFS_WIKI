<!-- TOC -->autoauto- [事件列表](#事件列表)auto- [获得焦点](#获得焦点)auto- [画面激活](#画面激活)auto- [回车或者其他键盘按键](#回车或者其他键盘按键)auto- [下拉列表初始化](#下拉列表初始化)auto- [数据加载事件](#数据加载事件)auto- [数据验证](#数据验证)auto- [显示后台代码返回的info_信息](#显示后台代码返回的info_信息)auto- [判定右键选择单行数据可用](#判定右键选择单行数据可用)autoauto<!-- /TOC -->
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