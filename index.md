## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/VNSHANPR/HC_TA/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

create table CARD_TRAN as (
select top 100 *,'TGT' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='7010'
union
select top 100 *,'WAL' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='7011'
union
select top 100 *,'APL' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='6927'
union
select top 100 *,'MAC' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='6059'
union
select top 100 *,'UNI' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='7238'
union
select top 100 *,'MOB' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='5655'
union
select top 100 *,'PEP' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='5877'
union
select top 100 *,'COK' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='5209'
union
select top 100 *,'AZ' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='6604'
union
select top 100 *,'VN' as MER_NAME from "BIUSER"."CARD_TRANSACTIONS_JULY_2020" where CT_MER_ID='7897');
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/VNSHANPR/HC_TA/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
