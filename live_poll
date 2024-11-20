import streamlit as st
import qrcode
from io import BytesIO
import random

# Function to generate a QR code
def generate_qr_code(url: str):
    qr = qrcode.QRCode(
        version=1,
        error_correction=qrcode.constants.ERROR_CORRECT_L,
        box_size=10,
        border=4,
    )
    qr.add_data(url)
    qr.make(fit=True)
    img = qr.make_image(fill_color="black", back_color="white")
    buffer = BytesIO()
    img.save(buffer, format="PNG")
    buffer.seek(0)
    return buffer

# Generate a unique 6-digit code for voting
if 'access_code' not in st.session_state:
    st.session_state.access_code = f"{random.randint(100000, 999999)}"

# Initialize poll data
if 'poll_data' not in st.session_state:
    st.session_state.poll_data = {
        "Option A": 0,
        "Option B": 0,
        "Option C": 0,
        "Option D": 0
    }

# Streamlit app title
st.title("Live Poll Presentation with QR Code & Access Code")

# Streamlit cloud or localhost base URL for the poll
base_url = "http://localhost:8501"  # Replace with your deployed Streamlit URL if deployed
poll_url = f"{base_url}/poll?code={st.session_state.access_code}"

# Generate QR code for the voting page
qr_code_image = generate_qr_code(poll_url)

# Presentation slide: Display the QR code and access code
st.write("### Scan this QR Code to Vote")
st.image(qr_code_image, caption="Scan the QR code to participate in the poll!", use_column_width=True)
st.write(f"Or enter this 6-digit code on the voting page: **{st.session_state.access_code}**")

# Display results dynamically
st.write("### Poll Results")
total_votes = sum(st.session_state.poll_data.values())

if total_votes > 0:
    for option, votes in st.session_state.poll_data.items():
        percentage = (votes / total_votes) * 100
        st.write(f"{option}: {votes} votes ({percentage:.2f}%)")
        st.progress(percentage / 100)
else:
    st.write("No votes yet. Be the first to vote!")

# Add footer
st.write("---")
st.write("Built with ❤️ using Streamlit.")

# Voting page logic
if st.experimental_get_query_params().get("code") == [st.session_state.access_code]:
    st.title("Vote Here!")
    with st.form("poll_form"):
        choice = st.radio(
            "Choose an option:",
            options=st.session_state.poll_data.keys()
        )
        submitted = st.form_submit_button("Submit Vote")

        if submitted:
            st.session_state.poll_data[choice] += 1
            st.success(f"Thank you for voting for {choice}!")
            st.experimental_rerun()

# Handle manual entry of access code
st.write("### Enter the Code to Vote")
access_code_input = st.text_input("Enter the 6-digit code:")
if access_code_input == st.session_state.access_code:
    st.success("Access code is correct! You can vote now.")
    st.experimental_set_query_params(code=st.session_state.access_code)
    st.experimental_rerun()
elif access_code_input:
    st.error("Invalid code. Please try again.")
