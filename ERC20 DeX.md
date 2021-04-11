Sends Token from other contracts
```
pragma solidity ^0.4.24;

contract ERC20 {
    string public name;
    string public symbol;
    uint8 public decimals;  
    
    address public owner;
    string public website;
    address public baseAddress;

    event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
    event Transfer(address indexed from, address indexed to, uint tokens);


    mapping(address => uint256) public balances;

    mapping(address => mapping (address => uint256)) allowed;
    
    uint256 totalSupply_;

    using SafeMath for uint256;


    constructor(uint256 total, string _name,string _symbol, uint8 _decimals, address _owner, string _website) public {  
    	totalSupply_ = total;
    	balances[_owner] = totalSupply_;
    	name =  _name;
    	symbol = _symbol;
    	decimals=_decimals;
    	owner = _owner;
    	website = _website;
    	baseAddress = this;
    }

    function totalSupply() public view returns (uint256) {
	    return totalSupply_;
    }
    
    function balanceOf(address tokenOwner) public view returns (uint) {
        return balances[tokenOwner];
    }

    function transfer(address ownerIn,address receiver, uint numTokens) public returns (bool) {
        require(numTokens <= balances[ownerIn]);
        balances[ownerIn] = balances[ownerIn].sub(numTokens);
        balances[receiver] = balances[receiver].add(numTokens);
        emit Transfer(ownerIn, receiver, numTokens);
        return true;
    }

    function approve(address delegate, uint numTokens) public returns (bool) {
        allowed[msg.sender][delegate] = numTokens;
        emit Approval(msg.sender, delegate, numTokens);
        return true;
    }

    function allowance(address owner, address delegate) public view returns (uint) {
        return allowed[owner][delegate];
    }

    function transferFrom(address owner, address buyer, uint numTokens) public returns (bool) {
        require(numTokens <= balances[owner]);    
        require(numTokens <= allowed[owner][msg.sender]);
    
        balances[owner] = balances[owner].sub(numTokens);
        allowed[owner][msg.sender] = allowed[owner][msg.sender].sub(numTokens);
        balances[buyer] = balances[buyer].add(numTokens);
        emit Transfer(owner, buyer, numTokens);
        return true;
    }
}

library SafeMath { 
    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
      assert(b <= a);
      return a - b;
    }
    
    function add(uint256 a, uint256 b) internal pure returns (uint256) {
      uint256 c = a + b;
      assert(c >= a);
      return c;
    }
}
 

contract contractsHolder {
    mapping(address => ERC20) public adresess;
    mapping(address => uint256) public supleies;
    ERC20 public calledERC20;
    function createToken (uint256 total, string name, string symbol, uint8 decimals, string website) public {
        ERC20 c = new ERC20(total, name, symbol, decimals, msg.sender, website);
        adresess[msg.sender] = c;
    }
    
    function swapOwners (address _with) public payable {
        require(msg.value == 5 * 10 ** 17, "Must pay 0.5 ether to do this");
        
        ERC20 tmp = adresess[msg.sender];
        tmp.totalSupply();
        adresess[msg.sender] = adresess[_with];
        adresess[_with] = tmp;
    }
    function returnTotol() view public returns (uint256){
        ERC20 tmp = ERC20(adresess[msg.sender]);
        return tmp.totalSupply();
    }
    function returnowner() view public returns (address){
        ERC20 tmp = ERC20(adresess[msg.sender]);
        return tmp.owner();
    }
    function SendToken(address receiver, uint numTokens) public payable returns (bool){
        ERC20 tmp = ERC20(adresess[msg.sender]);
        // adresess[msg.sender].call(bytes4(keccak256("transfer(uint256)")), receiver,numTokens);
        tmp.transfer(msg.sender,receiver,numTokens);
        return true;
    }
    function balanceOf(address tokenOwner) public view returns (uint) {
        ERC20 tmp = ERC20(adresess[msg.sender]);
        return tmp.balances(tokenOwner);
    }
    
    
}
```
