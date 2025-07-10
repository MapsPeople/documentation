---
cover: ../../.gitbook/assets/categories.jpg
coverY: 0
---

# Categories

While location types are used for styling locations on the map, **categories** are intended for **filtering** and **enhancing search experiences.** You can also assign locations to multiple categories if needed.&#x20;

For more info, [read here](interface-overview.md#categories).

### Subcategories

Subcategories add an extra layer to your categories, allowing you to create even more **advanced filtering** and search options for your end users, making the search experience easier and more efficient.

<figure><img src="../../.gitbook/assets/Clipboard-20250509-060658-633.gif" alt=""><figcaption><p>An example of using subcategories to add an extra filtering layer for "Restrooms," making it even easier for users to find their exactly what they are looking for</p></figcaption></figure>

You can set up "subcategories" in the CMS, under a category:

<figure><img src="../../.gitbook/assets/CleanShot 2025-05-12 at 15.43.53.png" alt=""><figcaption></figcaption></figure>

* When you assign a category as a child or subcategory, you cannot use it as a parent.
* You cannot delete a parent category without first removing its children.

{% hint style="success" %}
The Web SDK supports reading subcategory values using `childKeys`&#x20;
{% endhint %}

{% hint style="warning" %}
Mobile SDKs are not supported yet.
{% endhint %}
