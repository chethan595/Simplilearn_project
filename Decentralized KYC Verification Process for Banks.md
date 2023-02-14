// SPDX-License-Identifier: cs -1.0

pragma solidity >0.5.16;

contract Bankekyc {
address KYC_Admin;
constructor () {
    KYC_Admin = msg.sender;
}

modifier Admin () {
require (msg.sender == KYC_Admin, "Only Admin has access");
_;
}

    struct Bank_info{
        string Name;
        address Bank_Eth_Address;
        uint256 KYC_Count;
        bool Can_add_Customer;
        bool can_do_KYC;
    }
 struct Customer_info{
        string Customer_Name;
        string Customer_Data;
        address Customer_Bank;
        bool KYC_status;
    }
mapping (address => Bank_info) Bank;
mapping (string => Customer_info) Customer;

function AddNewBank (string memory _BName, address _BAddress) Admin public {
    require(_BAddress != address(0),"Not Valid address");
    Bank[_BAddress]=Bank_info(_BName, _BAddress, 0, true, true);
}
function AddNewCustomer (string memory _CName, string memory _CData) public {
    require(Bank[msg.sender].Can_add_Customer,"The Bank dosent have permission to Add customer");
    Customer[_CName]=Customer_info(_CName, _CData, msg.sender, false );
}
function Block_Bank_from_Adding_Customer (address _BankAddress) Admin public{
    require(_BankAddress != address(0),"Not Valid address");
    Bank[_BankAddress].Can_add_Customer=false;
}
function Allow_Bank_from_Adding_Customer (address _BAddress) Admin public {
    require(_BAddress != address(0),"Not Valid address");
    Bank[_BAddress].Can_add_Customer=true;
}
function Block_Bank_from_doing_KYC (address _BankAddress) Admin public{
    require(_BankAddress != address(0),"Not Valid address");
    Bank[_BankAddress].can_do_KYC=false;
}
function Allow_Bank_from_doing_KYC (address _BAddress) Admin public {
    require(_BAddress != address(0),"Not Valid address");
    Bank[_BAddress].can_do_KYC=true;
}
function Perform_KYC (string memory _CName) public {
    require(Bank[msg.sender].can_do_KYC,"The Bank dosent have permission to update KYC");
    Customer[_CName].KYC_status=true;
    Bank[msg.sender].KYC_Count++;
}
function View_Customer_Data (string memory _CName) public view returns (bool,string memory) {
    return (Customer[_CName].KYC_status, Customer[_CName].Customer_Data);
}
function View_Bank_Data (address _BName) public view returns (uint256) {
    return (Bank[_BName].KYC_Count);
}
 }

