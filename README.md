# BMI-calculator
bmi 측정하기
[app.py](https://github.com/user-attachments/files/28822237/app.py)
import streamlit as st

# 1. 페이지 기본 설정
st.set_page_config(
    page_title="건강 관리 웹사이트",
    page_icon="🏥",
    layout="centered"
)

# 2. 사이드바 메뉴 생성 (페이지 전환 역할)
st.sidebar.title("📌 메뉴 선택")
page = st.sidebar.radio("이동할 페이지를 선택하세요:", ["홈 화면", "BMI 계산기"])

# -------------------------------------------------------------
# [페이지 1] 홈 화면 로직
# -------------------------------------------------------------
if page == "홈 화면":
    st.title("🏥 나의 건강 관리 대시보드")
    st.markdown("""
    환영합니다! 이 웹사이트는 파이썬으로 제작된 건강 관리 서비스입니다.
    
    왼쪽 사이드바 메뉴에서 **'BMI 계산기'**를 선택하여 나의 비만도를 측정해 보세요.
    """)
    st.info("왼쪽 사이드바에서 원하는 기능을 선택하시면 해당 페이지로 이동합니다.")

# -------------------------------------------------------------
# [페이지 2] BMI 계산기 로직
# -------------------------------------------------------------
elif page == "BMI 계산기":
    # BMI 계산용 내부 함수 정의
    def calculate_bmi(weight, height_cm):
        height_m = height_cm / 100  # cm를 m로 변환
        return weight / (height_m ** 2)

    def get_bmi_category(bmi):
        # 대한비만학회 기준 적용
        if bmi < 18.5:
            return "저체중", "🔵"
        elif 18.5 <= bmi < 23:
            return "정상", "🟢"
        elif 23 <= bmi < 25:
            return "비만 전단계 (과체중)", "🟡"
        elif 25 <= bmi < 30:
            return "1단계 비만", "🟠"
        elif 30 <= bmi < 35:
            return "2단계 비만", "🔴"
        else:
            return "3단계 비만 (고도비만)", "🟤"

    st.title("🏃‍♂️ BMI (체질량 지수) 계산기")
    st.write("키와 몸무게를 입력하여 나의 BMI 지수를 확인해보세요!")

    # 사용자 입력 폼
    with st.form("bmi_form"):
        col1, col2 = st.columns(2)
        
        with col1:
            height = st.number_input("키 (cm)", min_value=50.0, max_value=300.0, value=170.0, step=0.1)
        with col2:
            weight = st.number_input("몸무게 (kg)", min_value=10.0, max_value=300.0, value=65.0, step=0.1)
            
        submitted = st.form_submit_button("계산하기")

    # 계산 버튼이 눌렸을 때 실행될 로직
    if submitted:
        bmi = calculate_bmi(weight, height)
        category, color_emoji = get_bmi_category(bmi)

        st.markdown("---")
        st.subheader("📊 측정 결과")
        
        # 결과를 시각적으로 표현
        st.metric(label="당신의 BMI 지수", value=f"{bmi:.2f}")
        
        if category == "정상":
            st.success(f"현재 **{category}** 상태입니다! {color_emoji} 훌륭하게 관리하고 계시네요.")
        elif category == "저체중":
            st.info(f"현재 **{category}** 상태입니다. {color_emoji} 균형 잡힌 영양 섭취가 필요합니다.")
        elif "비만" in category:
            st.warning(f"현재 **{category}** 상태입니다. {color_emoji} 꾸준한 운동과 식단 관리를 권장합니다.")
