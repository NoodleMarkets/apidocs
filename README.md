


## All requests:

### General cXML definitions:

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
- Description: Contains a <ShortName> element containing the product's name
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

Catalogs must contain a root <Index> element containing a single <SupplierID>
and multiple <IndexItem> elements, each describing a single product to added
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
