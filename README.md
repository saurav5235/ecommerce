**SMART CONTRACT FOR ECOMMERCE**

Structure of the Contract

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
        
Function to Register the Product

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
![Screenshot 2022-06-25 at 5 52 26 PM](https://user-images.githubusercontent.com/90019413/175773301-fdb13dfc-f2a5-4ff0-b593-bf5d30e4a60c.png) 

Function for buyer to buy the product

        function buy(uint _productId) payable public 
        {
            require(products[_productId-1].price==msg.value,"Please pay right amount of product");
            require(products[_productId-1].seller!=msg.sender,"Seller cannot buy products");
            products[_productId-1].buyer = msg.sender;
            emit bought(_productId,msg.sender);
        }
        
Function fot confirming the delivery

        function delivery(uint _productId) public
        {
            require(products[_productId-1].buyer==msg.sender,"Only buyer can collect the delivery");
            products[_productId-1].isDelivered = true;
            products[_productId-1].seller.transfer(products[_productId-1].price);
            emit delivered(_productId);
        }
