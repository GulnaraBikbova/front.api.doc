---
title: ���������� ����������� ����������� ������������ 
layout: default
---
# ���������� ����������� ����������� ������������ #

## ��������
 
������� ����������� ������������ ����� ���� ��������� ������ ������� ����������. 
��������, ���� ����� ������� ��������������� ������ ����� ��������� ������ ��� ��������� �������� ����� �������� ������.
����������, ��� ����� ����������� ���:

![DoCheque](../../img/chequeTaskProcessor/doCheque.png)

����� ����� ������������ � ������� �������� ������, � ����� � �������� ��������, �������, ������ X � Z �������, ���������� ����������� ��������� [`IChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor.htm) � ���������������� ��� ����������� ������ ������ API [`RegisterChequeTaskProcessor()`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_IOperationService_RegisterChequeTaskProcessor.htm):
```cs
var chequeTaskProcessor = new ChequeTaskProcessor()
PluginContext.Operations.RegisterChequeTaskProcessor(chequeTaskProcessor);
```

��� ������� ���������� [`IChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor.htm) ����� �������� ���������� �������� ��������: ������������ ����, ��������, �������, ������ X � Z ������. ��� ����� � ���� ������� ����� ������� ������ ���� ����������.
��������, ������� ������� - ������ ��������� ������ �� ����������� ���������� ������������ (��):
```cs
public void BeforeDoCheckAction(ChequeTask chequeTask, ICashRegisterInfo device, CashRegisterChequeExtensions chequeExtensions, IViewManager viewManager)
{
	// ��� �������: ��������� ��������� ����� ������ �� �������� ����������.
	if (device.IsVirtual)
		throw new Exception("Cash register is virtual. Please close order only on real device.");

	PluginContext.Log.InfoFormat("Before do cheque on cash register: {0} ({1})", device.FriendlyName, device.Id);
}
```
����� ��� �������� ������ �� ����������� �� iikoFront ������� ��������� �� ������ � ����� ��������� ��������:

![BeforeDoChequeException](../../img/chequeTaskProcessor/beforeDoChequeException.png)

������ ���������� ���������� [`IChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor.htm) � ��� ����������� ���� � ������� SDK SamplePlugin.


## ��������� [`IChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor.htm)

���������� ��������� ������� [`IChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor.htm).


### 1. [`BeforeDoCheckAction`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_BeforeDoCheckAction.htm)

[`BeforeDoCheckAction()`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_BeforeDoCheckAction.htm) - �������, ������� ����������� ����� ��������� ������������ ����. ��� ����� ���� �������� ������, ������� ������, ������, ������ �������. 
�������� �� ���������� - ��������� ����������� ���������� ��������, � ����� �������� � ��� �������������� ����������.

##### ��� �������� � ��� �������������� ����������?

���� 2 ������� �������� � ��� �������������� ����������:
- ����� ���������� ������� ���� ����������� � ����������� ����: *iikoOffice => ������������������ => �������� ����� => ���������� => ����: ���������� � ����������� ����*.
���������� ����� ������ ����� �������� � ����� ���� [`chequeTask.TextAfterCheque`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_Tasks_BillTask_TextAfterCheque.htm).

![AddChequeExtensionsIikoOffice](../../img/chequeTaskProcessor/addChequeExtensionsIikoOffice.png)

-  ������ ����� ��� ��������� ���� [`chequeExtensions.BeforeCheque`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_CashRegisterChequeExtensions_BeforeCheque.htm) � [`chequeExtensions.AfterCheque`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_CashRegisterChequeExtensions_AfterCheque.htm), ��������� ���� �������� [`chequeExtensions`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Data_Device_CashRegisterChequeExtensions.htm). 
��� ������ ����� ������������ � [`chequeTask.TextBeforeCheque`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_Tasks_BillTask_TextBeforeCheque.htm), [`chequeTask.TextAfterCheque`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_Tasks_BillTask_TextAfterCheque.htm) �������������� � �������� � �� ��� ������.
```cs
public void BeforeDoCheckAction(ChequeTask chequeTask, ICashRegisterInfo device, CashRegisterChequeExtensions chequeExtensions, IViewManager viewManager)
{
		...
		chequeExtensions.BeforeCheque.Add(new Center(new XElement("������")));
		chequeExtensions.AfterCheque.Add(new Center(new XElement("�����")));
		...
}
```

��������� ������� [`BeforeDoCheckAction()`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_BeforeDoCheckAction.htm):
- [`chequeTask`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Data_Device_Tasks_ChequeTask.htm) � *���������� �� ������*. 
��� ���������� �� ��������  ������, �������/���������, ������� � ������ (��. ������ [*API ������� ���������� ������������*](CashRegisters.html "������� ���������� ������������")).
- [`device`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Data_Device_ICashRegisterInfo.htm) � *���������� �� ����������� ������������, �� ������� ���������� �������� ������*.
- [`chequeExtensions`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Data_Device_CashRegisterChequeExtensions.htm) - *�������������� ���������� �� ����*. 
������ �������� ��� ���������� �� � ������ � ����� ���� ([`chequeExtensions.BeforeCheque`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_CashRegisterChequeExtensions_BeforeCheque.htm), [`chequeExtensions.AfterCheque`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_CashRegisterChequeExtensions_AfterCheque.htm)).
����� ������ ������������� [`chequeExtensions.PastOrderId`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_CashRegisterChequeExtensions_PastOrderId.htm) � ����� [`chequeExtensions.PastOrderNumber`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_CashRegisterChequeExtensions_PastOrderNumber.htm) ������, ��������� � ������� ��.
��� ���� ����������� ��� �������� ������ �� ������� ��.
- [`viewManager`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_UI_IViewManager.htm) � *���������� ����*.
��������� ���������� ��������������� ����� ���������� � iikoFront ���������� ���� (��. ������ [*API ���������� ����*](ViewManager.html "���������� ����")).


### 2. [`AfterDoCheckAction`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterDoCheckAction.htm)

[`AfterDoCheckAction()`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterDoCheckAction.htm) - �������, ������� ����������� ����� �������� ������������ ����. 
�������� �� ���������� - ��������� ����������� �������� ����� ������ ����, ��������, �������� � ������� �������� �������� �� ������� �������.
�������� [`result`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Data_Device_Results_PostResult.htm) ��������� ��������� ���������� �������� �� ��. 
[`result.Success = true`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_Results_PostResult_Success.htm) - �������� ��������� �������, ����� - �������.
���� �������� ��������� ��������� [`PostResult.Success = false`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_Results_PostResult_Success.htm), ����� ������ ����� ����� ������� � [`result.Message`](http://iiko.github.io/front.api.sdk/v6/html/P_Resto_Front_Api_V6_Data_Device_Results_PostResult_Message.htm).


### 3. [`BeforeXReport`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_BeforeXReport.htm)

[`BeforeXReport()`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_BeforeXReport.htm) - �������, ������� ����������� ����� ������� X-������.
� �������� ���������� ����� ���������� ���������� �����������, �� ������� ���������� ��������, ���������� ���� ��� ������ ��������� ��� ����� ������ � ������������ [`authUser`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Data_Security_IUser.htm), ������������ ������ X-������. 


### 4. [`AfterXReport`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterXReport.htm)

[`AfterXReport()`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterXReport.htm) - �������, ������� ����������� ����� ������ X-������.
��������� ���������� ������� [`AfterDoCheckAction`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterDoCheckAction.htm).


### 5. [`BeforeZReport`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_BeforeZReport.htm)

[`BeforeZReport`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_BeforeZReport.htm) - �������, ������� ����������� ����� ������� Z-������.
����������� ����������: ��, ������������ � ���������� ����.


### 6. [`AfterPayIn`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterPayIn.htm)

[`AfterPayIn`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterPayIn.htm) - �������, ������� ����������� ����� �������� �������� ������� � �����.
� ������� ����������: ��, ����� ����������� ������������ ��� ���������� ��������, ���������� ���� � ����� �������� �������, ������� ������� � �����.


### 7. [`AfterPayOut`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterPayOut.htm)

[`AfterPayOut`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor_AfterPayOut.htm) - �������, ������� ����������� ����� ������� �������� ������� �� �����.
� ������� ����������: ��, ����� ����������� ������������ ��� ���������� �������, ���������� ���� � ����� �������� �������, ������� ������� �� �����.


### �������������� ���������� [`IReadonlyChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IReadonlyChequeTaskProcessor.htm) � [`IEditableChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IEditableChequeTaskProcessor.htm)

������ � ������������� ����������� [`IChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IChequeTaskProcessor.htm), ����� ���� ����������� ��� ���������� [`IReadonlyChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IReadonlyChequeTaskProcessor.htm) � [`IEditableChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IEditableChequeTaskProcessor.htm), ������� ��������� ��������� ���� ������ ����� ����������� �������� � ������� �� �����.


�������������� ��� ����� �����, ��� ������������ ���������.
������� ����� ���� ���� � ���, ��� ��������� ��������� [`IReadonlyChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IReadonlyChequeTaskProcessor.htm) ������ ��� ������� �������� ������� �� ����� ��������������� ����� �������, � ��������� ��������� [`IEditableChequeTaskProcessor`](http://iiko.github.io/front.api.sdk/v6/html/T_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IEditableChequeTaskProcessor.htm) - �����, �� ���� ��������� [`ref estimatedSum`](http://iiko.github.io/front.api.sdk/v6/html/M_Resto_Front_Api_V6_Devices_ChequeTaskProcessor_IEditableChequeTaskProcessor_BeforePayOutSessionClosed.htm).
��� ������� ��� ����, ����� ��� �������� �� ������ ��� ������������ ����� �������.