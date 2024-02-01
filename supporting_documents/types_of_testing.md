# Types of Testing

## Response Testing (Return Value)

When invoking behaviour in code from a test the direct response can be tested against an expected known value.

```cs
public class Adder : IAdder
{
    public uint Add( uint firstNumber, uint secondNumber ) 
    {
        return firstNumber + secondNumber;
    }
}

public class AdderShould
{
    private readonly IAdder adder = new Adder();

    // Fact Example
    #[Fact]
    public void AddTwoNumbers()
    {
        // Given
        uint firstNumber = 1;
        uint secondNumber = 2; 
        uint expectedResult = 3;

        // When
        uint result = adder.Add( firstNumber, secondNumber )

        // Then
        Assert.Equal( expectedResult, result );
    }

    // Theory Example
    #[Theory]
    #[InlineData( 0, 0, 0 )]
    #[InlineData( 5, 5, 10 )]
    #[InlineData( 5, 7, 12 )]
    #[InlineData( 9, 5, 14 )]
    public void AddTwoNumbers( uint firstNumber, uint secondNumber, uint expectedResult )
    {
        // When
        uint result = adder.Add( firstNumber, secondNumber )

        // Then
        Assert.Equal( expectedResult, result );
    }
}
```

## Response Testing (Exception)

This also comes under the category of response testing as the response of the code under certain conditions will be to throw an exception.

```cs
public class Shop : IShop
{
    public Product Purchase() 
    {
        throw new NotImplementedException();
    }
}

public class ShopShould
{
    [Fact]
    public void PurchaseProduct()
    {
        // Given
        IShop shop = new Shop();

        // Then
        Assert.Throws<NotImplementedException>( () => shop.Purchase() );
    }
}
```

## Side Effect Testing

When invoking behaviour in code from a test the side effects of that code can be tested against an expected known values.

```cs
public class DriverModel : IDriverModel
{
    public string Name { get; private set; }
    public uint PenaltyPoints { get; private set; }

    public void UpdateLicenseInformation ( string name, uint penaltyPoints ) 
    {
        Name = name;
        PenaltyPoints = penaltyPoints;
    }
}

public class DriverModelShould
{
    [Theory]
    [InlineData("Jeff Heel", 3)]
    [InlineData("Wendy Street", 0)]
    [InlineData("Joe Bloggs", 9)]
    public void UpdateLicenseInformationGivenLicenseDetails( string name, uint penaltyPoints )
    {
        // Given
        IDriverModel driverModel = new DriverModel();

        // When
        driverModel.UpdateLicenseInformation( name, penaltyPoints );

        // Then
        Assert.Equal( name, driverModel.Name );
        Assert.Equal( penaltyPoints, driverModel.PenaltyPoints );
    }
}
```

## Interaction Testing (Test Doubles)

When invoking behaviour in code from a test the method calls of dependencies can be tested to demonstrate:

- The number of calls a dependency’s method made (Once, Many or Exactly)
- The method calls a dependency didn’t make

```cs
public interface IShopController
{
    IProduct Purchase();
    uint Sell( IProduct product );
    void CalculateRemainingStock();
    void DisplaySales();
}

public class ShopService : IShopService
{
    private shopController;

    public ShopService(IShopController shopController)
    {
        this.shopController = shopController;
    }

    public uint TotalSales { get; private set; }

    public void ReplaceStockAndSellProduct() 
    {
        IProduct product = shopController.Purchase();

        TotalSales += shopController.Sell( product );

        if( TotalSales > 5 ) 
        {
            shopController.CalculateRemainingStock();
        }
    }

    public void DisplaySales() {
        shopController.DisplaySales();
    }
}

public class ShopServiceShould 
{
    // Simple mocking example
    [Fact]
    public void DisplaySales() 
    {
        // Given
        // Mock
        Mock<IShoppingController> shoppingController = new();
        shoppingController.Setup( sc => sc.DisplaySales() );

        IShoppingService shoppingService = new ShoppingService(shoppingController.Object);

        // When
        shoppingService.DisplaySales();

        // Then
        shoppingController.VerifyAll();
        // Spy
        shoppingController.VerifyNoOtherCalls();
    }

    // Complex stubbing example checking the default path
    [Fact]
    public void ReplaceStockAndSellProduct()
    {
        // Given
        MockSequence sequence = new();

        // Dummy
        Mock<IProduct> product = new();

        // Stub
        Mock<IShoppingController> shoppingController = new();
        shoppingController.InSequence(sequence).Setup( sc => sc.Purchase() ).Returns(product.Object);
        shoppingController.InSequence(sequence).Setup( sc => sc.Sell( product.Object ) ).Returns( 1 );

        IShoppingService shoppingService = new ShoppingService(shoppingController.Object);

        // When
        shoppingService.ReplaceStockAndSellProduct();

        // Then
        shoppingController.VerifyAll();
        // Spy
        shoppingController.VerifyNoOtherCalls();
    }

    // Complex example using stubbing to test code paths
    [Fact]
    public void ReplaceStockAndSellProductWhereTotalSalesAreMoreThanFive()
    {
        // Given
        MockSequence sequence = new();

        // Dummy
        Mock<IProduct> product = new();

        // Stub
        Mock<IShoppingController> shoppingController = new();
        shoppingController.InSequence(sequence).Setup( sc => sc.Purchase() ).Returns(product.Object);
        shoppingController.InSequence(sequence).Setup( sc => sc.Sell( product.Object ) ).Returns( 6 );
        // Mock
        shoppingController.InSequence(sequence).Setup( sc => sc.shopController.CalculateRemainingStock() );

        IShoppingService shoppingService = new ShoppingService(shoppingController.Object);

        // When
        shoppingService.ReplaceStockAndSellProduct();

        // Then
        shoppingController.VerifyAll();
        // Spy
        shoppingController.VerifyNoOtherCalls();
    }
}

public class ShopControllerShould
{
    // Contains implementation detail unit tests of shop controller
    // using the response and side effect test types
    ...
}
```
