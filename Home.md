![Are you mocking me?](http://lh5.ggpht.com/_iTnDnty4mRk/TFsS15Nuu7I/AAAAAAAAANM/EuX4rAhiF0s/FakingMock.png)

##It's faking amazing!

    // Creating a fake object is just dead easy!
    // No mocks, no stubs, everything's a fake!
    var lollipop = A.Fake<ICandy>();
    var shop = A.Fake<ICandyShop>();
    
    // To set up a call to return a value is also simple:
    A.CallTo(() => shop.GetTopSellingCandy()).Returns(lollipop);
    
    // Use your fake as you would an actual instance of the faked type.
    var developer = new SweetTooth();
    developer.BuyTastiestCandy(shop);
    
    // Asserting uses the exact same syntax as when configuring calls,
    // no need to teach yourself another syntax.
    A.CallTo(() => shop.BuyCandy(lollipop)).MustHaveHappened();

In this example the lollipop instance is used as a stub and the shop instance is used as a mock but there's no need to know the difference, just fake it! Easy!