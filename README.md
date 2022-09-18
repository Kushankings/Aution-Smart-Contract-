# Aution-Smart-Contract
for Fundraising

Consider the problem of auction or penny social. ” It is a
conventional approach used for fundraising for a cause. The organizers collect items to be auctioned off
for raising funds. Before the auction, the items for auctions are received and arranged each with a bowl
to place the bid. A chairperson is a special person among the organizers. She/he heads the effort and is
the only person who can determine the winner by random drawing at the end of the auction. A set of
bidders buy sheets of tickets with their money. The bidder’s sheet has a stub that identifies the bidder’s
number, and tokens bought.
The bidders examine the items to bid, place the one or more tickets in the bowl in front of the items they
desire to bid for until all the tickets are used. After the auction period ends the chairperson, collects the
bowls, randomly selects a ticket from each item’s bowl to determine the winning bidder for that item. The
item is transferred to the winning bidder. Total money collected is the fund raised by the penny social
auction.

# Functionality

The functions are similar to the Ballot of our lessons. Constructor that initializes the owner, register that
allows (decentralized person) to register online to get the tokens and start bidding, bid function that lets
a person bid, and finally revealWinner, to randomly choose the winner for the item auctioned. Here is
our design. Always remember to design first. The Auction smart contract has Item and Person structs
and other data items such as array of Items, array of Persons, array of winners, mappings, and
beneficiary address.

Assumption : Currently some assumption are taken like fixed amount of items.

# Language

Solidity

# Software 

Remix

# Code


***
pragma solidity ^0.4.17;
contract Auction {
    
   
    struct Item {
        uint itemId; // id of the item
        uint[] itemTokens;  //tokens bid in favor of the item
       
    }
    
   
    struct Person {
        uint remainingTokens; 
        uint personId; 
        address addr;
    }
 
    mapping(address => Person) tokenDetails; 
    Person [4] bidders;
    
    Item [3] public items;
    address[3] public winners;
    address public beneficiary;
    
    uint bidderCount=0;
  

    function Auction() public payable{    //constructor
                
       beneficiary = msg.sender;
        uint[] memory emptyArray;
        items[0] = Item({itemId:0,itemTokens:emptyArray});
        items[1] = Item({itemId:1,itemTokens:emptyArray});
        items[2] =Item({itemId:2,itemTokens:emptyArray});
    }
    

    function register() public payable{
        
        
        bidders[bidderCount].personId = bidderCount;
        bidders[bidderCount].addr = msg.sender;
        bidders[bidderCount].remainingTokens = 5;
        tokenDetails[msg.sender]=bidders[bidderCount];
        bidderCount++;
    }
    
    function bid(uint _itemId, uint _count) public payable{
      
        if( tokenDetails[msg.sender].remainingTokens < _count || tokenDetails[msg.sender].remainingTokens == 0 ) { revert(); }
        if( _itemId > 2 ) { revert(); }
        
        uint balance= tokenDetails[msg.sender].remainingTokens - _count ;
       
        
        tokenDetails[msg.sender].remainingTokens=balance;
        bidders[tokenDetails[msg.sender].personId].remainingTokens=balance;
        Item storage bidItem = items[_itemId];
        for(uint i=0; i<_count;i++) {
            bidItem.itemTokens.push(tokenDetails[msg.sender].personId);    
        }
    }
    

    modifier onlyOwner {
        require (msg.sender == beneficiary);
        _;
    }
    
    
    function revealWinners() public onlyOwner{
        

        for (uint id = 0; id < 3; id++) {
            Item storage currentItem=items[id];
            if(currentItem.itemTokens.length != 0){
          uint randomIndex = (block.number / currentItem.itemTokens.length)% currentItem.itemTokens.length; 
          uint winnerId = currentItem.itemTokens[randomIndex];
                
           winners[id] = bidders[winnerId].addr;
                                 
            }
        }
    } 

    function getPersonDetails(uint id) public constant returns(uint,uint,address){
        return (bidders[id].remainingTokens,bidders[id].personId,bidders[id].addr);
    }

}

***
