Upgradeable contract using Separate logic and data solution
```

contract StorageContract {
uint public val;
function setval(uint _val) public returns (bool success){
val = _val;
return true;
}
}
contract contractsHolder  {
    StorageContract public dataContract;
    
    constructor (uint val) public{
        StorageContract c = new StorageContract();
        dataContract=c;
        c.setval(val);
    }
    
    function setValUp(uint val) public returns (bool){
        StorageContract tmp = StorageContract(dataContract);
        tmp.setval(val);
        return true;
    }
     
     function getValUp() public view returns  (uint){
        StorageContract tmp = StorageContract(dataContract);
        return tmp.val();
   
    }
}

```
