# responsive-email-dynamic
Responsive email template that dynamically hides table rows (via CSS) in email body table if field has no value.

Sometimes an organisation needs to send templated email for which data is pulled from database by templating engine but there is no easy way to parse content to assert for null values and remove them from the text so fields with no value are still included and appear empty which makes overall customer experience rather poor.

This responsive html email template uses ccs trick at the client side to hide rows from the table if value is null. The challenge was that not all email clients support css3 and most do not allow javascript in the content so selective hiding based on value was not possible. The trick I came up with was to hide content of the table row using unpredictably constructed class names during parsing.

In the template the class name is prepended with xxx followed substitution token that parser will replace with some value. 
If the field value is null then the resulting class name will be 'xxx' which has corresponding style to hide it. If field value is not null the resulting name will be 'xxxSomeValue' that style does not exist hence default style will be inherited from the document and used.

As an example I used this trick with Siebel that substitutes fileds that are surrounded by square brackets and that worked rather well. 

## An example

The template contains:
```html
<tr class="xxx[field 1]">
  <td valign="top" class="label">Field 1:</td>
  <td valign="top" class="value">[field 1]</td>
</tr>
```

The parser will replace [filed 1] with value 'INC001234' resulting in:

```html
<tr class="xxxINC001234">
  <td valign="top" class="label">Field 1:</td>
  <td valign="top" class="value">INC001234</td>
</tr>
```
If the [field 1] was null the resulting class name would be:

```html
<tr class="xxx">
  <td valign="top" class="label">Field 1:</td>
  <td valign="top" class="value"></td>
</tr>
```
Since xxx is a class defined in `<style>` of the email the `<tr>` would be hidden.

## Possible side effects
If the value of the filed contains spaces and multiple words it is possible that some class that was predefined in html template will match some words giving undersired result. Imagine that the field value was 'This is a test'. The resulting class name would be `class="xxxThis is a test"`.   
