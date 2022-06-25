**SMART CONTRACT FOR ECOMMERCE**

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract Ecommerce
    {
        struct product
        {
            string productName;
            string description;
            address payable seller;
            uint productId;
            uint price;
            address buyer;
            bool isDelivered;
        }

        event registered(string productName,uint productId,address seller);
        event bought(uint productId,address buyer);
        event delivered(uint productId);

        uint count = 1;

        product[] public products;

        function registerProduct(string memory _productName,string memory _description,uint _price) public
        {
            require(_price>0,"price should not be less than or equal to zero");
            product memory productDetails;
            productDetails.productName = _productName;
            productDetails.description = _description;
            productDetails.price = _price * 10**18;
            productDetails.seller = payable(msg.sender);
            productDetails.productId = count;
            products.push(productDetails);
            count++;
            emit registered(_productName,productDetails.productId,msg.sender);
        }

        function buy(uint _productId) payable public 
        {
            require(products[_productId-1].price==msg.value,"Please pay right amount of product");
            require(products[_productId-1].seller!=msg.sender,"Seller cannot buy products");
            products[_productId-1].buyer = msg.sender;
            emit bought(_productId,msg.sender);
        }

        function delivery(uint _productId) public
        {
            require(products[_productId-1].buyer==msg.sender,"Only buyer can collect the delivery");
            products[_productId-1].isDelivered = true;
            products[_productId-1].seller.transfer(products[_productId-1].price);
            emit delivered(_productId);
        }
    }
