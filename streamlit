import streamlit as st
import random
import urllib.parse
from datetime import date

st.set_page_config(page_title="Trean Cool & Ice", page_icon="🧊", layout="centered")

if "products" not in st.session_state:
    st.session_state.products = []

if "cart" not in st.session_state:
    st.session_state.cart = []

if "delivery_date" not in st.session_state:
    st.session_state.delivery_date = "Pending Admin Confirmation"

ADMIN_WHATSAPP = "2349066407555"
ADMIN_PASSWORD = "trean123"

st.title("🧊 Trean Cool & Ice")
st.caption("Chilled Zobo Drinks & Premium Ice Blocks")

with st.container():
    st.info("""
    💳 **Company Payment Details**  
    * **Bank:** OPay  
    * **Account Number:** 9066407555  
    * **Account Name:** Trean Cool & Ice  
    * **Official WhatsApp:** 09066407555  
    """)

st.sidebar.title("🔒 Admin Control")
admin_pass = st.sidebar.text_input("Enter Admin Password", type="password")

if admin_pass == ADMIN_PASSWORD:
    st.sidebar.success("Admin Portal Unlocked!")
    st.sidebar.subheader("👑 Admin Control Panel")
    
    new_delivery_date = st.sidebar.date_input("Set Delivery Date for Orders", value=date.today())
    if st.sidebar.button("Update Delivery Date"):
        st.session_state.delivery_date = new_delivery_date.strftime("%d %b %Y")
        st.sidebar.success(f"Delivery date set to: {st.session_state.delivery_date}")

    st.sidebar.markdown("---")
    st.sidebar.subheader("Upload Product to App")
    with st.sidebar.form("upload_form", clear_on_submit=True):
        p_name = st.text_input("Product Name")
        p_price = st.number_input("Price (₦)", min_value=100, step=50)
        p_img = st.file_uploader("Product Image", type=["jpg", "png", "jpeg"])
        submit_upload = st.form_submit_button("Upload Product to Store")

        if submit_upload:
            if p_name and p_price and p_img:
                st.session_state.products.append({
                    "name": p_name,
                    "price": p_price,
                    "image": p_img.read()
                })
                st.sidebar.success(f"Uploaded '{p_name}' successfully!")
                st.rerun()
            else:
                st.sidebar.error("Please fill in all product fields!")

st.header("🛒 Store Products")

if not st.session_state.products:
    st.warning("Store is currently updating. Admin will add products shortly!")
else:
    cols = st.columns(2)
    for index, prod in enumerate(st.session_state.products):
        col = cols[index % 2]
        with col:
            st.image(prod["image"], use_column_width=True)
            st.subheader(prod["name"])
            st.write(f"**₦{prod['price']:,}**")
            if st.button(f"Add to Order", key=f"add_{index}"):
                st.session_state.cart.append(prod)
                st.toast(f"Added {prod['name']} to cart!")

st.markdown("---")
st.header("📋 Shopping Cart & Checkout")

if not st.session_state.cart:
    st.info("Your cart is empty. Click 'Add to Order' on products above.")
else:
    total_amount = sum(item["price"] for item in st.session_state.cart)
    st.write("### Items Selected:")
    for idx, item in enumerate(st.session_state.cart):
        st.write(f"{idx+1}. **{item['name']}** — ₦{item['price']:,}")
    
    st.subheader(f"Total Amount: ₦{total_amount:,}")
    
    if st.button("Clear Cart"):
        st.session_state.cart = []
        st.rerun()

    st.markdown("---")
    st.subheader("Customer Shipping Details")
    cust_name = st.text_input("Full Name")
    cust_phone = st.text_input("Phone Number")
    cust_address = st.text_area("Delivery Address")
    pay_method = st.selectbox("Payment Method", ["OPay Bank Transfer (9066407555)", "Debit Card Payment"])

    if st.button("Complete Order & Generate Receipt", type="primary"):
        if not cust_name or not cust_phone or not cust_address:
            st.error("Please fill in all customer details before proceeding!")
        else:
            tracking_code = f"TRN-{random.randint(100000, 900000)}"
            
            st.success("🎉 Order Processed Successfully!")
            st.markdown("### 🧾 Official Purchase Receipt")
            st.markdown(f"""
            * **Customer Name:** {cust_name}
            * **Phone:** {cust_phone}
            * **Address:** {cust_address}
            * **Payment Method:** {pay_method}
            * **Expected Delivery Date:** {st.session_state.delivery_date}
            * **Total Paid:** **₦{total_amount:,}**
            """)
            
            st.warning(f"🔑 **ADMIN TRACKING CODE:** `{tracking_code}`")

            admin_msg = (
                f"🚨 *NEW ORDER RECEIVED*\n\n"
                f"*Customer:* {cust_name}\n"
                f"*Phone:* {cust_phone}\n"
                f"*Address:* {cust_address}\n"
                f"*Payment Method:* {pay_method}\n"
                f"*Total:* ₦{total_amount:,}\n"
                f"*Scheduled Delivery:* {st.session_state.delivery_date}\n"
                f"*Admin Tracking Code:* {tracking_code}"
            )
            admin_url = f"https://wa.me/{ADMIN_WHATSAPP}?text={urllib.parse.quote(admin_msg)}"

            cust_phone_formatted = cust_phone.replace(" ", "").replace("+", "")
            if cust_phone_formatted.startswith("0"):
                cust_phone_formatted = "234" + cust_phone_formatted[1:]
            
            cust_msg = (
                f"✅ *YOUR ORDER HAS BEEN PLACED!*\n\n"
                f"Hello {cust_name}, thank you for ordering from *Trean Cool & Ice*!\n\n"
                f"We received your payment via *{pay_method}*.\n"
                f"📅 *Expected Delivery Date:* {st.session_state.delivery_date}\n"
                f"📍 *Delivery Address:* {cust_address}\n"
                f"💰 *Total Paid:* ₦{total_amount:,}\n\n"
                f"Reply to this message if you have any questions!"
            )
            cust_url = f"https://wa.me/{cust_phone_formatted}?text={urllib.parse.quote(cust_msg)}"

            col1, col2 = st.columns(2)
            with col1:
                st.link_button("📱 Notify Admin on WhatsApp", admin_url)
            with col2:
                st.link_button("📱 Send Customer Receipt", cust_url)
