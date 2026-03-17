# Conditionally PopUp LOV Value Color on Oracle APEX
Enhance your Popup LOV UI by dynamically applying colors based on values! This guide walks you through setting up a query-based Popup LOV and using JavaScript to conditionally style list items.

## Step # 01: Create Popup LOV Item

- **Item Name:** `P59_QUERY_BASED`
- **Type:** Popup LOV

#### LOV SQL Query
```sql
SELECT S.ATTRIBUTE_NAME||' ('||I.GENERIC_ITEM_NAME||')', S.ID
FROM ATTRIBUTE_SETUPS S,
     GENERIC_ITEMS I
WHERE S.GENERIC_ITEM_ID = I.ID
```
## Step # 02 : Add Dynamic Action (Page Load)
- **Event :** Page Load
- **True Action :** Execute JavaScript Code
#### JavaScript Code
``` JavaScript
(function(){
  var dialogObservers = {};
  function colorList($container){
    if (!$container || $container.length === 0) return;
    var $items = $container.find(
      'li, .a-List-item, .a-PopupLOV-results li, .apex-item-result, .t-List-item'
    );

    if ($items.length === 0) {
      $items = $container.find('a, span, div').filter(function(){
        var t = $(this).text().trim();
        return t.length > 0 && t.length < 200;
      });
    }

    $items.each(function(){
      var $el = $(this);
      var txt = $el.text().trim();
      if (!txt) return;

      $el.removeClass('lov-red lov-green lov-blue');

      if (txt.includes("Application")) {
        $el.addClass("u-color-5");
      } 
      else if (txt.includes("Desktop")) {
        $el.addClass("u-color-7");
      }
      else if (txt.includes("Laptop")) {
        $el.addClass("u-color-12");
      }
      else if (txt.includes("Mini Desktop")) {
        $el.addClass("u-color-13");
      }
      else if (txt.includes("Networking Device")) {
        $el.addClass("u-color-14");
      }
      else if (txt.includes("Printer")) {
        $el.addClass("u-color-18");
      }
      else if (txt.includes("Router")) {
        $el.addClass("u-color-23");
      }
      else if (txt.includes("TAB")) {
        $el.addClass("u-color-43");
      }
    });
  }

  function attachDialogObserver($dlg){
    if (!$dlg || $dlg.length === 0) return;
    var dlgId = $dlg.attr('id') || ('lovdlg_' + Math.random().toString(36).substr(2,9));
    $dlg.attr('id', dlgId);
    if (dialogObservers[dlgId]) return;

    setTimeout(function(){ colorList($dlg); }, 50);

    var obs = new MutationObserver(function(){
      colorList($dlg);
    });
    obs.observe($dlg[0], { childList: true, subtree: true, characterData: true });
    dialogObservers[dlgId] = obs;
  }

  $(document).on("apexafterlovopen", function(){
    var $dlg = $(".a-PopupLOV-dialog");
    attachDialogObserver($dlg);
  });

  var globalObs = new MutationObserver(function(muts){
    muts.forEach(function(m){
      $(m.addedNodes).each(function(){
        var $node = $(this);
        if ($node.hasClass("a-PopupLOV-dialog")){
          attachDialogObserver($node);
        }
      });
    });
  });

  globalObs.observe(document.body, { childList: true, subtree: true });

  console.log("LOV Coloring for ALL LOVs activated");
})();
```

## Tips
- Customize txt.includes("Value") based on your own data
- Use APEX utility classes like u-color-* for styling

## Result
- Popup LOV values will be color-coded dynamically for better UX

![Popup LOV Preview](preview.png)
