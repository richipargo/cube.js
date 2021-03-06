---
title: "Explore Page"
order: 4
---

On the Explore page, the user either creates a new chart or updates the existing one. To do so, the user should be able to select measures, dimensions, apply filters if needed, and select a chart type. We also need to provide a way to save a new chart to the dashboard or to update the existing one when editing.

The image below shows how the page will look after the styling is applied.

![](/images/4-screenshot-1.png)

Let’s break down the page into components. We need a modal window to let the user set or update the chart’s title—`<TitleModal />`. Next, we have a page’s header—`<PageHeader />`. Inside the page’s header to the right, we have a button that is used to either add a chart to the dashboard or update it. It is a regular `<Button />` component from antd’s UI kit. To the left—a page’s title, which either displays a chart’s title when editing or just says ‘Explore’ when we are building a new one. Let’s call it an `<ExploreTitle />` component.

Finally, we have a complex component to render a query builder and a chart itself. The dashboard app already generated this component for us—`<ExploreQueryBuilder />`. We’ll take a closer look at this component, its structure, and how we will customize it in the next section. Now let’s create the above components and add them to the Explore page.

<IMAGE WITH THE COMPONENTS>

The `<TitleModal />` component is already generated by our dashboard app. And since we’ve updated some style variables in the previous chapter, it already fits into our design, so we don’t need to update it anymore.

We don’t have a `<PageHeader />` component yet, so let’s create one. This component is going to be reused on the Dashboard page and should act as a container for our title and the button.

Create a `src/components/PageHeader.js` file the following content.

```javascript
import React from "react";
import { Row, Col  } from "antd";
import styled from 'styled-components';

const StyledRow = styled(Row)`
  padding: 23px 28px 13px 28px;
  background: white;
`

const ButtonsCol = styled(Col)`
  text-align: right;
`

const PageHeader = ({ title, button, noBorder }) => (
  <StyledRow>
    <Col span={12}>
      {title}
    </Col>
    <ButtonsCol span={12}>
      {button}
    </ButtonsCol>
  </StyledRow>
);

export default PageHeader;
```

Next, let’s create the `<ExploreTitle />` component. It will display the chart’s title if the `itemTitle` variable is not null, otherwise it will just display “Explore” as a page title.

Create the `src/components/ExploreTitle.js` file the following content.

```javascript
import React from "react";
import { Typography } from "antd";
import { Link } from "react-router-dom";
import styled from 'styled-components';

const StyledLink = styled(Link)`
  && {
    color: #D5D5DE;
    &:hover {
      color: #7A77FF;
    }
  }
`

const StyledDash = styled.span`
  color: #D5D5DE;
`

const ExploreTitle = ({ itemTitle }) => (
  <Typography.Title level={4}>
    { itemTitle ?
    (
      <span>
        <StyledLink to="/">Dashboard</StyledLink>
        <StyledDash> — </StyledDash>
        {itemTitle}
     </span>
    ) : "Explore" }
  </Typography.Title>
);

export default ExploreTitle;
```

Finally, we need to update the Explore page with new components and a new layout. First, import the newly created components and the `isQueryPresent` helper method from `@cubejs-client/react`. We’re going to use the `isQueryPresent` method to disable the “Add to Dashboard” button when a query is not present.

Add the following lines to the imports block in `src/pages/ExplorePage.js`.

```javascript
import { isQueryPresent } from "@cubejs-client/react";
import PageHeader from "../components/PageHeader.js";
import ExploreTitle from "../components/ExploreTitle.js";
```

Next, update the layout of the page. Update the entire `return` block in `src/pages/ExplorePage.js` with the following content.

```diff
+  return (
+    <div>
+      <TitleModal
+        history={history}
+        itemId={itemId}
+        titleModalVisible={titleModalVisible}
+        setTitleModalVisible={setTitleModalVisible}
+        setAddingToDashboard={setAddingToDashboard}
+        finalVizState={finalVizState}
+        setTitle={setTitle}
+        finalTitle={finalTitle}
+      />
+      <PageHeader
+        title={<ExploreTitle itemId={itemId} />}
+        button={
+          <Button
+            key="button"
+            type="primary"
+            loading={addingToDashboard}
+            disabled={!isQueryPresent(finalVizState.query || {})}
+            onClick={() => setTitleModalVisible(true)}
+          >
+            {itemId ? "Update" : "Add to Dashboard"}
+          </Button>
+        }
+      />
+      <ExploreQueryBuilder
+        vizState={finalVizState}
+        setVizState={setVizState}
+      />
+    </div>
+  );
-  return (
-    <div>
-      <TitleModal
-        history={history}
-        itemId={itemId}
-        titleModalVisible={titleModalVisible}
-        setTitleModalVisible={setTitleModalVisible}
-        setAddingToDashboard={setAddingToDashboard}
-        finalVizState={finalVizState}
-        setTitle={setTitle}
-        finalTitle={finalTitle}
-      />
-      <ExploreQueryBuilder
-        vizState={finalVizState}
-        setVizState={setVizState}
-        chartExtra={[
-          <Button
-            key="button"
-            type="primary"
-            loading={addingToDashboard}
-            onClick={() => setTitleModalVisible(true)}
-          >
-            {itemId ? "Update" : "Add to Dashboard"}
-          </Button>
-        ]}
-      />
-    </div>
-  );
```

That gives us a layout we need for an Explore page. In the next two chapters we’ll customize the query builder and the chart itself.
