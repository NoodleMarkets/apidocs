


## All requests:

### General cXML definitions:

#### Address:

A billing, shipping, or contact address.

Definition:

```xml
<Address isoCountryCode="XX" addressID="XXXXXX">
  <Name xml:lang="en"/>
  <PostalAddress name="XXXXXX">
    <DeliverTo/>
    <Street/>
    <City/>
    <State/>
    <PostalCode/>
    <Country isoCountryCode="XX"/>
  </PostalAddress>
  <Email name="XXXXX"/>
  <Phone name="XXXXX">
    <TelephoneNumber>
      <CountryCode isoCountryCode="XX"/>
      <AreaOrCityCode/>
      <Number/>
    </TelephoneNumber>
  </Phone>
</Address>
```

Example:

```xml
<Address isoCountryCode="US" addressID="4988">
  <Name xml:lang="en"/>Noodle Markets Office</Name>
  <PostalAddress name="default">
    <DeliverTo>Ashwin C.</DeliverTo>
    <DeliverTo>Noodle Markets</DeliverTo>
    <Street>860 Broadway</Street>
    <Street>3rd Floor</Street>
    <City>New York</City>
    <State>NY</State>
    <PostalCode>10023</PostalCode>
    <Country isoCountryCode="US">United States</Country>
  </PostalAddress>
  <Email name="default">ashwin+2834@noodle.com</Email>
  <Phone name="work">
    <TelephoneNumber>
      <CountryCode isoCountryCode="US">1</CountryCode>
      <AreaOrCityCode>123</AreaOrCityCode>
      <Number>4567890</Number>
    </TelephoneNumber>
  </Phone>
</Address>
```

#### ItemID:

Uniquely identifies a single product.

Definition:

```xml
<ItemID>
  <SupplierPartID/>
  <SupplierPartAuxiliaryID/>
</ItemID>
```

Parameters:

- SupplierPartID: A supplier-unique identifier for this product. Likely a SKU
    or other internal identifier.
- SupplierPartAuxiliaryID: Currently unused. Should be a blank string.

Example:

```xml
<ItemID>
  <SupplierPartID>PK12-446538</SupplierPartID>
  <SupplierPartAuxiliaryID/>
</ItemID>
```

#### ItemDetail:

Describes details of a product.

Definition:

```xml
<ItemDetail>
  <UnitPrice>
    <Money currency="USD"/>
  </UnitPrice>
  <Description xml:lang="en">
    <ShortName/>
    <!-- Longer description -->
  </Description>
  <UnitOfMeasure/>
  <Classification/>
<ItemDetail/>
```

Parameters:

- UnitPrice/Money: Product unit price. Must specify "USD" for the "currency"
    attribute.
- Description: Contains a `<ShortName>` element containing the product's name
    along with longer description as text.
- UnitOfMeasure: Currently unused.
- Classification: Currently unused.

Example:

```xml
<ItemDetail>
  <UnitPrice>
    <Money currency="USD">3.22</Money>
  </UnitPrice>
  <Description xml:lang="en">
    <ShortName>Comfort Grip Ballpoint Pen, 12pk</ShortName>
    Reliable from the first mark to the last. Soft textured Comfort Grip for
    smooth, comfortable writing. Oil-based ink dries quickly, ensuring
    smear-free performance.
  </Description>
  <UnitOfMeasure/>
  <Classification/>
</ItemDetail>
```

## Updating a catalog:

Supplier submits a cXML CatalogUploadRequest with cXML catalog as MIME
attachment. Catalog definitions are applied as incremental updates and
a supplier may have a maximum of one catalog active at any given time.

Request format:

```xml
<!-- MIME header -->
<cXML>
  <!-- cXML request header -->
  <Request>
    <CatalogUploadRequest operation="update">
      <Attachment>
        <URL/>
      </Attachment>
      <Notification>
        <Email/>
        <URLPost enabled="true"/>
      </Notification>
    </CatalogUploadRequest>
  </Request>
</cXML>
<!-- MIME attachment -->
```

Parameters:

- Attachment/URL: Must use "cid" scheme, points to MIME attachment
    Content-ID.
- Notification/Email: Optional email address to receive notification once
    catalog validation and processing are finished.
- Notification/URLPost: Has one attribute "enabled". If "true", will post a
    catalog status notification to the status update URL provided during
    registration.

Example:

```xml
Content-type: multipart/related;
  boundary="XXXXboundary text";
  type="text/xml";
  start="<part1.12345666@procurek12.com>";
--XXXXboundary text
Content-type: text/xml; charset=UTF-8
Content-ID: <part1.12345666@procurek12.com>
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE cXML SYSTEM "http://xml.cxml.org/schemas/cXML/1.2.014/cXML.dtd">
<cXML timestamp="2000-12-28T16:56:03-08:00" payloadID="12345666@procurek12.com">
  <Header>
    <Sender>
      <Credential domain="DUNS">
        <Identity><!-- your pk12 API username --></Identity>
        <SharedSecret><!-- your pk12 API secret --></SharedSecret>
      </Credential>
    </Sender>
 </Header>
 <Request>
   <CatalogUploadRequest operation="update">
      <Attachment>
        <URL>cid:part2.12345666@procurek12.com</URL>
      </Attachment>
      <Notification>
        <Email>email@supplier.com</Email>
        <URLPost enabled="true"/>
      </Notification>
    </CatalogUploadRequest>
  </Request>
</cXML>
--XXXXboundary text
Content-type: text/xml;
Content-Disposition: attachment; filename=catalog.xml
Content-ID: <part2.12345666@procurek12.com>
Content-length: XXX
<Index loadmode="Incremental">
  <SupplierID>28</SupplierID>
  <IndexItem>
    <IndexItemAdd>
      <ItemID>
        <SupplierPartID>PK12-446538</SupplierPartID>
        <SupplierPartAuxiliaryID/>
      </ItemID>
      <ItemDetail>
        <UnitPrice>
          <Money currency="USD">3.22</Money>
        </UnitPrice>
        <Description xml:lang="en">
          <ShortName>Comfort Grip Ballpoint Pen, 12pk</ShortName>
          Reliable from the first mark to the last. Soft textured Comfort Grip for
          smooth, comfortable writing. Oil-based ink dries quickly, ensuring
          smear-free performance.
        </Description>
        <UnitOfMeasure/>
        <Classification/>
      </ItemDetail>
      <IndexItemDetail>
        <LeadTime>14</LeadTime>
        <Extrinsic name="ImageURL">http://procurek12.com/img/pk12-446538.jpg</Extrinsic>
      </IndexItemDetail>
    </IndexItemAdd>
  </IndexItem>
  <IndexItem>
    <IndexItemDelete>
      <ItemID>
        <SupplierPartID>PK12-835644</SupplierPartID>
        <SupplierPartAuxiliaryID/>
      </ItemID>
    </IndexItemDelete>
  </IndexItem>
</Index>
--XXXXboundary text
```

Although a response will be returned right away if the submitted document is
valid cXML, it will take some time to validate and process the attached catalog
data for import. Expect about 30 minutes per 100,000 products.

Response format:

If accepted, the endpoint will return a cXML `<Response>` element with a 201
status code.

```xml
<Response>
  <Status code="201" text="Accepted">The catalog upload request is processing.</Status>
</Response>
```

### Catalog definition:

Catalogs must contain a root `<Index>` element containing a single `<SupplierID>`
and multiple `<IndexItem>` elements, each describing a single product to added
or removed from the catalog.

Definition:

```xml
<Index loadmode="Incremental">
  <SupplierID/>
  <IndexItem/>
  <IndexItem/>
  ...
</Index>
```

Parameters:

- SupplierID: Your ProcureK12 supplier ID, provided during registration.
- IndexItem: Contains either `<IndexItemAdd>` or `<IndexItemDelete>` to add or
    remove an item. See definitions below.

Example:

```xml
<Index loadmode="Incremental">
  <SupplierID>28</SupplierID>
  <IndexItem>
    <IndexItemAdd>
      <ItemID>
        <SupplierPartID>PK12-446538</SupplierPartID>
        <SupplierPartAuxiliaryID/>
      </ItemID>
      <ItemDetail>
        <UnitPrice>
          <Money currency="USD">3.22</Money>
        </UnitPrice>
        <Description xml:lang="en">
          <ShortName>Comfort Grip Ballpoint Pen, 12pk</ShortName>
          Reliable from the first mark to the last. Soft textured Comfort Grip for
          smooth, comfortable writing. Oil-based ink dries quickly, ensuring
          smear-free performance.
        </Description>
        <UnitOfMeasure/>
        <Classification/>
      </ItemDetail>
      <IndexItemDetail>
        <LeadTime>14</LeadTime>
        <Extrinsic name="ImageURL">http://procurek12.com/img/pk12-446538.jpg</Extrinsic>
      </IndexItemDetail>
    </IndexItemAdd>
  </IndexItem>
  <IndexItem>
    <IndexItemDelete>
      <ItemID>
        <SupplierPartID>PK12-835644</SupplierPartID>
        <SupplierPartAuxiliaryID/>
      </ItemID>
    </IndexItemDelete>
  </IndexItem>
</Index>
```

#### Adding items:

Use `<IndexItemAdd>` to add an item to the catalog.

Definition:

```xml
<IndexItem>
  <IndexItemAdd>
    <ItemID/>
    <ItemDetail/>
    <IndexItemDetail>
      <LeadTime/>
      <Extrinsic name="ImageURL"/>
    </IndexItemDetail>
  </IndexItemAdd>
</IndexItem>
```

Parameters:

- ItemID: Product identifier. See `<ItemID>` definition above.
- ItemDetail: Product details. See `<ItemDetail>` definition above.
- IndexItemDetail/LeadTime: Number of days customer can expect to receive this
  product.
- IndexItemDetail/Extrinsic[@name="ImageURL"]: Should contain an image URL
  at least 1080x1080px. All products require an image, and this URL will be
  accessed during catalog validation. If no image can be found the catalog
  import will fail.

#### Removing items:

Use `<IndexItemDelete>` to remove an item from the catalog.

Definition:

```xml
<IndexItem>
  <IndexItemDelete>
    <ItemID/>
  </IndexItemDelete>
</IndexItem>
```

Parameters:

- ItemID: Product identifier. See `<ItemID>` definition above.


## Accepting orders:

To receive orders, suppliers must implement an endpoint capable of receiving
a cXML `<OrderRequest>`. Unless otherwise specified, orders will be submitted
after payment is received and should be fulfilled immediately.

Request format:

```xml
<cXML>
  <!-- cXML request header -->
  <Request>
    <OrderRequest/>
  </Request>
</cXML>
<!-- MIME attachment -->
```

Example:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE cXML SYSTEM "http://xml.cxml.org/schemas/cXML/1.2.014/cXML.dtd">
<cXML timestamp="2018-09-27T16:56:03-08:00" payloadID="66654321@procurek12.com">
  <Header>
    <Sender>
      <Credential domain="DUNS">
        <Identity><!-- your pk12 API username --></Identity>
        <SharedSecret><!-- your pk12 API secret --></SharedSecret>
      </Credential>
    </Sender>
 </Header>
 <Request>
    <OrderRequest>
      <OrderRequestHeader orderID="61" orderDate="2018-09-27T16:56:03-08:00" type="new">
        <Total>
          <Money currency="USD">749.99</Money>
        </Total>
        <ShipTo>
          <Address isoCountryCode="US" addressID="4988">
            <Name xml:lang="en"/>Noodle Markets Office</Name>
            <PostalAddress name="default">
              <DeliverTo>Ashwin C.</DeliverTo>
              <DeliverTo>Noodle Markets</DeliverTo>
              <Street>860 Broadway</Street>
              <Street>3rd Floor</Street>
              <City>New York</City>
              <State>NY</State>
              <PostalCode>10023</PostalCode>
              <Country isoCountryCode="US">United States</Country>
            </PostalAddress>
            <Email name="default">ashwin+2834@noodle.com</Email>
            <Phone name="work">
              <TelephoneNumber>
                <CountryCode isoCountryCode="US">1</CountryCode>
                <AreaOrCityCode>123</AreaOrCityCode>
                <Number>4567890</Number>
              </TelephoneNumber>
            </Phone>
          </Address>
        </ShipTo>
        <BillTo>
          <Address isoCountryCode="US" addressID="4988">
            <Name xml:lang="en"/>Noodle Markets Office</Name>
            <PostalAddress name="default">
              <DeliverTo>Ashwin C.</DeliverTo>
              <DeliverTo>Noodle Markets</DeliverTo>
              <Street>860 Broadway</Street>
              <Street>3rd Floor</Street>
              <City>New York</City>
              <State>NY</State>
              <PostalCode>10023</PostalCode>
              <Country isoCountryCode="US">United States</Country>
            </PostalAddress>
          </Address>
        </BillTo>
        <Shipping>
          <Money currency="USD">9.95</Money>
          <Description xml:lang="en-US">Standard US</Description>
        </Shipping>
        <Tax>
          <Money currency="USD">24.76</Money>
          <Description xml:lang="en">State, county, and city tax</Description>
        </Tax>
      </OrderRequestHeader>
      <ItemOut quantity="10" lineNumber="1">
        <ItemID>
          <SupplierPartID>PK12-835644</SupplierPartID>
        </ItemID>
      </ItemOut>
      <ItemOut quantity="15" lineNumber="2">
        <ItemID>
          <SupplierPartID>PK12-446538</SupplierPartID>
        </ItemID>
      </ItemOut>
    </OrderRequest>
  </Request>
</cXML>
```

### OrderRequest:

`<OrderRequest>` must contain both and `<OrderRequestHeader`>
and at least one `<ItemOut>` describing a line item in a purchase order.

Definition:

```xml
<OrderRequest>
  <OrderRequestHeader/>
  <ItemOut/>
  <ItemOut/>
  ...
</OrderRequest>
```

### OrderRequestHeader:

Contains general information about the order, including totals, contact info,
and shipping.

Definition:

```xml
<OrderRequestHeader orderID="XXXX" orderDate="YYYY-MM-DDTHH:MM:SS" type="new">
  <Total>
    <Money currency="USD"/>
  </Total>
  <ShipTo>
    <Address isoCountryCode="US"/>
  </ShipTo>
  <BillTo>
    <Address isoCountryCode="US"/>
  </BillTo>
  <Shipping>
    <Money currency="USD"/>
    <Description xml:lang="en"/>
  </Shipping>
  <Tax>
    <Money currency="USD"/>
    <Description xml:lang="en"/>
  </Tax>
</OrderRequestHeader>
```

Parameters:

- OrderRequestHeader/@orderID: The ProcureK12 order ID. This is the ID that can
    be used to provide order status updates.
- Total/Money: The total order amount in USD, including tax and shipping.
- ShipTo/Address: The order shipping address. See the `<Address>` definition
    above.
- BillTo/Address: The order billing address. See the `<Address>` definition
    above.
- Shipping/Money: The total shipping cost without taxes. If taxes were charged
    on shipping they will be included in the `<Tax>` element.
- Shipping/Description: A description of the shipping method chosen.
- Tax/Money: The total tax collected in USD.
- Tax/Description: A description of what taxes were collected.

Example:

```xml
<OrderRequestHeader orderID="60" orderDate="2018-09-27T08:49:09+07:00" type="new">
  <Total>
    <Money currency="USD">350.50</Money>
  </Total>
  <ShipTo>
    <Address isoCountryCode="US" addressID="4988">
      <Name xml:lang="en"/>Noodle Markets Office</Name>
      <PostalAddress name="default">
        <DeliverTo>Ashwin C.</DeliverTo>
        <DeliverTo>Noodle Markets</DeliverTo>
        <Street>860 Broadway</Street>
        <Street>3rd Floor</Street>
        <City>New York</City>
        <State>NY</State>
        <PostalCode>10023</PostalCode>
        <Country isoCountryCode="US">United States</Country>
      </PostalAddress>
      <Email name="default">ashwin+2834@noodle.com</Email>
      <Phone name="work">
        <TelephoneNumber>
          <CountryCode isoCountryCode="US">1</CountryCode>
          <AreaOrCityCode>123</AreaOrCityCode>
          <Number>4567890</Number>
        </TelephoneNumber>
      </Phone>
    </Address>
  </ShipTo>
  <BillTo>
    <Address isoCountryCode="US" addressID="4988">
      <Name xml:lang="en"/>Noodle Markets Office</Name>
      <PostalAddress name="default">
        <DeliverTo>Ashwin C.</DeliverTo>
        <DeliverTo>Noodle Markets</DeliverTo>
        <Street>860 Broadway</Street>
        <Street>3rd Floor</Street>
        <City>New York</City>
        <State>NY</State>
        <PostalCode>10023</PostalCode>
        <Country isoCountryCode="US">United States</Country>
      </PostalAddress>
    </Address>
  </BillTo>
  <Shipping>
    <Money currency="USD">9.95</Money>
    <Description xml:lang="en-US">Standard US</Description>
  </Shipping>
  <Tax>
    <Money currency="USD">24.76</Money>
    <Description xml:lang="en">State, county, and city tax</Description>
  </Tax>
</OrderRequestHeader>
```

### ItemOut:

Describes a single line item in an order.

Definition:

```xml
<ItemOut quantity="XX" lineNumber="XX">
  <ItemID>
    <SupplierPartID/>
  </ItemID>
</ItemOut>
```

Parameters:

- @quantity: The quantity of an item purchased.
- @lineNumber: A unique line number per `<ItemOut>`, starting with 1.
- ItemID/SupplierPartID: The unique product ID provided by the supplier during
    the catalog upload process.

Example:

```xml
<ItemOut quantity="10" lineNumber="1">
  <ItemID>
    <SupplierPartID>PK12-835644</SupplierPartID>
  </ItemID>
</ItemOut>
```
