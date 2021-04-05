##Index price : median of last, best bid, best offer

BTC

Binance TUSD
Binance USDC
Bitstamp USD
Bittrex USD
Coinbase USD
Coinbase USDC
Kraken USD
Okex USDC
FTX USD
FTXUS USD

Test with:
==================================================================================================================

pragma solidity ^0.6.0;

import "https://github.com/smartcontractkit/chainlink/blob/develop/evm-contracts/src/v0.6/ChainlinkClient.sol";

contract APIConsumer is ChainlinkClient {
  
    uint256 public btcPrice;
    
    address public oracle;
    bytes32 public jobId;
    uint256 public fee;
    string public info = "getbtc";
    
    /**
     * Network: BSC
     * Oracle: 0x074715cc07fC0Df9c617F22971Fb2Ff1b1f57278
     * Job ID: 4ec420e8f838483e9ce39c26d5523c3b
     * Fee: 0.02 LINK
     */
     
    constructor() public {
        setChainlinkToken(0x404460C6A5EdE2D891e8297795264fDe62ADBB75);
        oracle = 0x074715cc07fC0Df9c617F22971Fb2Ff1b1f57278;
        jobId = "4ec420e8f838483e9ce39c26d5523c3b";
        fee = 0.02 * 10 ** 18; // 0.02 LINK
    }
    
    /**
     * Create a Chainlink request to retrieve API response.
     */
    function requestBTC() public returns (bytes32 requestId) 
    {
        Chainlink.Request memory request = buildChainlinkRequest(jobId, address(this), this.fulfill.selector);

        return sendChainlinkRequestTo(oracle, request, fee);
    }
    
    /**
     * Receive the response in the form of uint256 with 12 decimals. So div with 10**12.
     */ 
    function fulfill(bytes32 _requestId, uint256 _btcPrice) public recordChainlinkFulfillment(_requestId)
    {
        btcPrice = _btcPrice;
    }
    
    function withdrawLink() external {
        LinkTokenInterface linkToken = LinkTokenInterface(chainlinkTokenAddress());
        require(linkToken.transfer(msg.sender, linkToken.balanceOf(address(this))), "Unable to transfer");
    }
}
