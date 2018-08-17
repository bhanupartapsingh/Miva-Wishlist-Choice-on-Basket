# Miva Wishlist Choice on Basket
This Snippet is used to select or create wishlist on basket page and will move items to wishlist
Step 1: Add below script to basket page
<script>
		jQuery(document).ready(function() {
			jQuery('#js-open-select-wishlist').magnificPopup({
				focus : '#l-Customer_LoginEmail',
				items : {
					src : jQuery('#js-select-wishlist'),
					type : 'inline'
				}
			});
			jQuery('#l-wishlist').change(function() {
				if (jQuery('option:selected', this).val()) {
					if (jQuery('option:selected', this).val() == 'crwish') {
						jQuery('#creatWishlist').show();
					} else {
						jQuery('#creatWishlist').hide();
						window.location = jQuery('#js-open-select-wishlist').attr('href') + '&WishList_ID=' + jQuery('option:selected', this).val();
					}
				} else {
					jQuery('#creatWishlist').hide();
				}
			});
			jQuery('#createWishlistAjax').click(function() {
				var frmData = jQuery('#createWishlistAjaxForm').serialize();
				var formUrl = jQuery('#createWishlistAjaxForm').attr('action');
				var randomNo = Math.ceil(Math.random() * 1000000);
				if (/\?/.test(formUrl)) {
					formUrl = formUrl + '&v=' + randomNo;
				} else {
					formUrl = formUrl + '?v=' + randomNo;
				};
				jQuery.ajax({
					method : "POST",
					url : formUrl,
					data : frmData,
				}).done(function(returndata) {
					if (isNaN(returndata)) {
						Alert('Error in creation of Wishlist');
					} else {
						window.location = jQuery('#js-open-select-wishlist').attr('href') + '&WishList_ID=' + returndata;
					}
				});
			});
		});
	</script>
  
 2. Step 2: create below code on basket footer
 
  <div id="js-select-wishlist" class="row mfp-hide forgot-password">
	<h4>Create New or Select Wishlist:</h4>
	<select id="l-wishlist">
		<option value="">Select</option>
		<mvt:foreach iterator="wishlist" array="customerwishlists:wishlists">
			<option value="&mvte:wishlist:id;">&mvte:wishlist:title;</option>
		</mvt:foreach>
		<option value="crwish">Create New Wishlist</option>
	</select>
	<div id="creatWishlist" style="display:none">
	
	<form action="<mvt:if expr="g.Secure">&mvte:urls:AJAX:secure;<mvt:else>&mvte:urls:AJAX:auto;</mvt:if>" method="post" style="padding: 0px;" id="createWishlistAjaxForm" autocomplete="off"  class="column whole medium-full medium-centered">
		<br/>
	  <div class="&mvte:global:WishList_Title_Row; form-row">
		<label for="l-WishList_Title" class="required">Title:</label>
		<input type="text" name="WishList_Title" id="l-WishList_Title" value="&mvte:global:WishList_Title;" required />
		<input type="hidden" name="Action"  value="CRWISH" />
	   </div>
	  <div class="form-row align-right">
		<input type="button" value="Save" id="createWishlistAjax" class="button button-square button-medium bg-sky">
	  </div>
	</form>
    </div>
</div>

3. Step 3: Create a new page with page code AJAX and add code below

<mvt:if expr="g.Action EQ 'CRWISH'">
<Mvt:ASSIGN NAME = "l.wishlist" 			VALUE = "''"/>
<Mvt:ASSIGN NAME = "l.wishlist:cust_id" 	VALUE = "g.Basket:cust_id"/>
<Mvt:ASSIGN NAME = "l.wishlist:title" 	VALUE = "g.WishList_Title"/>
<Mvt:ASSIGN NAME = "l.wishlist:notes"		VALUE = "g.WishList_Notes"/>
<Mvt:ASSIGN NAME = "l.wishlist:shared"	VALUE = "g.WishList_Shared"/>
<mvt:do file="g.Module_Feature_WSH_DB" name="l.success" value="WishList_Insert( l.wishlist )" />
<Mvt:ASSIGN NAME = "g.WishList_ID" 		VALUE = "l.wishlist:id"/>
&mvt:global:WishList_ID;<mvt:exit/>
</mvt:if>
