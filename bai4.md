Đáp án: Phương án B

Phân Tích Phương Án B (Tối ưu nhất)
Phương án B áp dụng đầy đủ các kỹ năng đặt prompt kiểm thử logic:

Role (Vai trò): Xác định rõ "Senior QA Engineer" -

vai trò này định hướng AI tiếp cận bài toán từ góc độ kiểm thử chuyên nghiệp,

chủ động tìm kiếm edge case thay vì chỉ viết happy path test.
Chỉ định kỹ thuật test cụ thể: Yêu cầu đích danh @ParameterizedTest và @ValueSource -

AI không tự chọn viết từng method test riêng lẻ mà phải dùng đúng kỹ thuật

cho phép kiểm thử nhiều giá trị trong một method, giảm code trùng lặp.
Liệt kê đầy đủ Edge Case cần bao phủ: Đây là điểm then chốt nhất -

thay vì nói chung chung "test các trường hợp không hợp lệ", phương án B liệt kê

7 trường hợp biên cụ thể: null, rỗng, khoảng trắng, quá ngắn (7 ký tự),

quá dài (21 ký tự), thiếu chữ hoa, thiếu số, thiếu ký tự đặc biệt.

Mỗi trường hợp kiểm tra đúng một quy tắc nghiệp vụ.
Format (Định dạng): Yêu cầu mã nguồn Java JUnit 5 hoàn chỉnh kèm giải thích tiếng Việt -

kết quả có thể đưa vào project ngay mà không cần chỉnh sửa thủ công.


Phân Tích Phương Án A (Loại trừ)
Phương án A là Zero-shot prompt thiếu hầu hết thành phần kỹ thuật:

Không có kỹ thuật test cụ thể: Không nhắc đến Parameterized Test -

AI sẽ viết từng method riêng lẻ cho mỗi trường hợp, code dài và trùng lặp,

không tận dụng được tính năng của JUnit 5.
Không liệt kê edge case: "Hợp lệ và không hợp lệ" quá mơ hồ -

AI tự chọn vài trường hợp theo suy đoán, thường bỏ sót các biên nguy hiểm

như null, khoảng trắng, hoặc mật khẩu đúng độ dài nhưng thiếu ký tự đặc biệt.
Không có Role: Không có góc nhìn QA chuyên nghiệp -

AI phản hồi như developer viết test cho có, không như người chuyên tìm lỗi.
Không có Format: Không yêu cầu giải thích -

kết quả có thể chỉ là code không có chú thích, khó bảo trì về sau.


Phân Tích Phương Án C (Loại trừ)
Phương án C không phải prompt kiểm thử mà là prompt né tránh kiểm thử:

Hiểu sai mục tiêu hoàn toàn: Yêu cầu viết lại implementation thay vì viết test -

đây là tư duy ngược: thay vì đảm bảo logic đúng bằng test,

lại cố gắng rút ngắn code để không phải test,

một regex phức tạp che giấu logic còn khó debug hơn code tường minh.
Regex ngắn không đồng nghĩa đúng: Một regex dài 50 ký tự bao phủ đủ quy tắc

vẫn cần unit test để xác nhận nó không có lỗi ở ranh giới -

bỏ test không giải quyết được vấn đề, chỉ chuyển rủi ro sang môi trường Production.
Vi phạm nguyên tắc kiểm thử cơ bản: Trong hệ thống xác thực mật khẩu,

bỏ qua unit test là rủi ro bảo mật nghiêm trọng -

một lỗi logic nhỏ trong PasswordValidator có thể cho phép mật khẩu yếu lọt qua.


Mã Nguồn JUnit 5 Do AI Sinh Ra
PasswordValidator (class cần test):
javaimport java.util.regex.Pattern;

/**
* Validator kiểm tra độ mạnh mật khẩu theo các quy tắc nghiệp vụ.
* Tách logic validate thành các method riêng để dễ test từng quy tắc độc lập.
  */
  public class PasswordValidator {

  // Độ dài tối thiểu và tối đa theo quy tắc nghiệp vụ
  private static final int MIN_LENGTH = 8;
  private static final int MAX_LENGTH = 20;

  // Pattern kiểm tra sự tồn tại của từng loại ký tự
  private static final Pattern HAS_UPPERCASE      = Pattern.compile("[A-Z]");
  private static final Pattern HAS_LOWERCASE      = Pattern.compile("[a-z]");
  private static final Pattern HAS_DIGIT          = Pattern.compile("[0-9]");
  private static final Pattern HAS_SPECIAL_CHAR   = Pattern.compile("[@#$%]");
  private static final Pattern HAS_WHITESPACE     = Pattern.compile("\\s");

  /**
    * Kiểm tra mật khẩu có hợp lệ theo toàn bộ quy tắc không.
    *
    * @param password Mật khẩu cần kiểm tra
    * @return true nếu hợp lệ, false nếu vi phạm bất kỳ quy tắc nào
      */
      public boolean isValid(String password) {
      if (password == null) return false;
      if (HAS_WHITESPACE.matcher(password).find()) return false;
      if (password.length() < MIN_LENGTH || password.length() > MAX_LENGTH) return false;
      if (!HAS_UPPERCASE.matcher(password).find()) return false;
      if (!HAS_LOWERCASE.matcher(password).find()) return false;
      if (!HAS_DIGIT.matcher(password).find()) return false;
      if (!HAS_SPECIAL_CHAR.matcher(password).find()) return false;
      return true;
      }
      }

JUnit 5 Test Class:
javaimport org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.NullSource;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

/**
* Bộ test kiểm thử toàn diện PasswordValidator theo từng nhóm quy tắc nghiệp vụ.
* Sử dụng @ParameterizedTest để kiểm thử nhiều giá trị trong một method,
* giảm code trùng lặp và tăng độ bao phủ edge case.
  */
  @DisplayName("PasswordValidator - Kiểm thử toàn diện quy tắc mật khẩu")
  class PasswordValidatorTest {

  private PasswordValidator validator;

  @BeforeEach
  void setUp() {
  // Khởi tạo mới validator trước mỗi test để đảm bảo test độc lập
  validator = new PasswordValidator();
  }

  // ===================== NHÓM 1: MẬT KHẨU HỢP LỆ =====================

  /**
    * Kiểm tra các mật khẩu đáp ứng đủ mọi quy tắc phải được chấp nhận.
    * Dùng @ValueSource để test nhiều mật khẩu hợp lệ trong một method.
      */
      @ParameterizedTest(name = "Mật khẩu hợp lệ: \"{0}\" phải được chấp nhận")
      @ValueSource(strings = {
      "Abcdef1@",           // Đúng 8 ký tự - biên dưới tối thiểu
      "Abcdefgh1@",         // Độ dài trung bình hợp lệ
      "Abcdefghijklmno1@#", // Gần biên trên - 19 ký tự
      "A1@bcdefghijklmnopqt", // Đúng 20 ký tự - biên trên tối đa
      "ValidPass1#",        // Mật khẩu thông thường đầy đủ yếu tố
      "MyP@ssw0rd",         // Mật khẩu phổ biến có đủ yếu tố
      "Secure$Pass99",      // Chứa ký tự đặc biệt $
      "Hello%World1",       // Chứa ký tự đặc biệt %
      })
      @DisplayName("Mật khẩu hợp lệ phải trả về true")
      void validPasswords_shouldReturnTrue(String password) {
      assertTrue(validator.isValid(password),
      "Mật khẩu '" + password + "' phải được chấp nhận nhưng bị từ chối.");
      }

  // ===================== NHÓM 2: NULL VÀ RỖNG =====================

  /**
    * Kiểm tra mật khẩu null phải bị từ chối.
    * Dùng @NullSource để truyền null vào @ParameterizedTest một cách tường minh.
      */
      @ParameterizedTest(name = "Mật khẩu null phải bị từ chối")
      @NullSource
      @DisplayName("Mật khẩu null phải trả về false")
      void nullPassword_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu null phải bị từ chối.");
      }

  /**
    * Kiểm tra chuỗi rỗng và chuỗi chỉ chứa khoảng trắng phải bị từ chối.
      */
      @ParameterizedTest(name = "Mật khẩu rỗng hoặc khoảng trắng: \"{0}\" phải bị từ chối")
      @ValueSource(strings = {
      "",          // Chuỗi rỗng hoàn toàn
      " ",         // Một khoảng trắng
      "   ",       // Nhiều khoảng trắng
      })
      @DisplayName("Mật khẩu rỗng và khoảng trắng phải trả về false")
      void emptyOrBlankPasswords_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu rỗng hoặc chỉ khoảng trắng phải bị từ chối.");
      }

  // ===================== NHÓM 3: VI PHẠM ĐỘ DÀI =====================

  /**
    * Kiểm tra mật khẩu quá ngắn (dưới 8 ký tự) phải bị từ chối.
    * Bao phủ các biên: 1 ký tự, 7 ký tự (biên dưới -1).
      */
      @ParameterizedTest(name = "Mật khẩu quá ngắn: \"{0}\" ({0.length()} ký tự) phải bị từ chối")
      @ValueSource(strings = {
      "A1@b",      // 4 ký tự - quá ngắn
      "A1@bcde",   // 7 ký tự - ngay biên dưới tối thiểu
      "A",         // 1 ký tự - cực ngắn
      })
      @DisplayName("Mật khẩu dưới 8 ký tự phải trả về false")
      void tooShortPasswords_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu '" + password + "' có " + password.length() +
      " ký tự, phải bị từ chối vì dưới 8 ký tự.");
      }

  /**
    * Kiểm tra mật khẩu quá dài (trên 20 ký tự) phải bị từ chối.
    * Bao phủ biên trên: 21 ký tự (biên trên +1) và dài hơn.
      */
      @ParameterizedTest(name = "Mật khẩu quá dài phải bị từ chối")
      @ValueSource(strings = {
      "A1@bcdefghijklmnopqrs",   // 21 ký tự - ngay biên trên tối đa
      "A1@bcdefghijklmnopqrstu", // 23 ký tự - vượt biên trên
      })
      @DisplayName("Mật khẩu trên 20 ký tự phải trả về false")
      void tooLongPasswords_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu có " + password.length() +
      " ký tự phải bị từ chối vì vượt quá 20 ký tự.");
      }

  // ===================== NHÓM 4: THIẾU TỪNG THÀNH PHẦN BẮT BUỘC =====================

  /**
    * Kiểm tra mật khẩu thiếu chữ hoa phải bị từ chối.
    * Các mật khẩu này đủ độ dài và có các thành phần khác nhưng thiếu chữ hoa.
      */
      @ParameterizedTest(name = "Mật khẩu thiếu chữ hoa: \"{0}\" phải bị từ chối")
      @ValueSource(strings = {
      "abcdef1@",      // Đủ điều kiện khác nhưng không có chữ hoa
      "abcdefgh1@#",   // Dài hơn nhưng vẫn thiếu chữ hoa
      })
      @DisplayName("Mật khẩu thiếu chữ hoa phải trả về false")
      void passwordsWithoutUppercase_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu '" + password + "' thiếu chữ hoa phải bị từ chối.");
      }

  /**
    * Kiểm tra mật khẩu thiếu chữ thường phải bị từ chối.
      */
      @ParameterizedTest(name = "Mật khẩu thiếu chữ thường: \"{0}\" phải bị từ chối")
      @ValueSource(strings = {
      "ABCDEF1@",      // Toàn chữ hoa, không có chữ thường
      "ABCDEFGH1@#",   // Dài hơn nhưng vẫn thiếu chữ thường
      })
      @DisplayName("Mật khẩu thiếu chữ thường phải trả về false")
      void passwordsWithoutLowercase_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu '" + password + "' thiếu chữ thường phải bị từ chối.");
      }

  /**
    * Kiểm tra mật khẩu thiếu chữ số phải bị từ chối.
      */
      @ParameterizedTest(name = "Mật khẩu thiếu chữ số: \"{0}\" phải bị từ chối")
      @ValueSource(strings = {
      "Abcdefg@",      // Đủ điều kiện khác nhưng không có chữ số
      "AbcdefgH@#",    // Dài hơn nhưng vẫn thiếu chữ số
      })
      @DisplayName("Mật khẩu thiếu chữ số phải trả về false")
      void passwordsWithoutDigit_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu '" + password + "' thiếu chữ số phải bị từ chối.");
      }

  /**
    * Kiểm tra mật khẩu thiếu ký tự đặc biệt phải bị từ chối.
      */
      @ParameterizedTest(name = "Mật khẩu thiếu ký tự đặc biệt: \"{0}\" phải bị từ chối")
      @ValueSource(strings = {
      "Abcdef12",      // Đủ điều kiện khác nhưng không có ký tự đặc biệt
      "AbcdefgH12",    // Dài hơn nhưng vẫn thiếu ký tự đặc biệt
      })
      @DisplayName("Mật khẩu thiếu ký tự đặc biệt (@#$%) phải trả về false")
      void passwordsWithoutSpecialChar_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu '" + password + "' thiếu ký tự đặc biệt phải bị từ chối.");
      }

  // ===================== NHÓM 5: CHỨA KHOẢNG TRẮNG =====================

  /**
    * Kiểm tra mật khẩu chứa khoảng trắng ở bất kỳ vị trí nào phải bị từ chối.
    * Khoảng trắng trong mật khẩu gây rủi ro bảo mật và lỗi xử lý ở nhiều hệ thống.
      */
      @ParameterizedTest(name = "Mật khẩu chứa khoảng trắng: \"{0}\" phải bị từ chối")
      @ValueSource(strings = {
      "Abcdef1@ ",     // Khoảng trắng ở cuối
      " Abcdef1@",     // Khoảng trắng ở đầu
      "Abc def1@",     // Khoảng trắng ở giữa
      "Abcdef1@\t",    // Tab character (dạng khoảng trắng đặc biệt)
      })
      @DisplayName("Mật khẩu chứa khoảng trắng phải trả về false")
      void passwordsWithWhitespace_shouldReturnFalse(String password) {
      assertFalse(validator.isValid(password),
      "Mật khẩu chứa khoảng trắng phải bị từ chối.");
      }

  // ===================== NHÓM 6: KIỂM TRA BIÊN CHÍNH XÁC =====================

  /**
    * Kiểm tra chính xác biên dưới: 8 ký tự phải hợp lệ, 7 ký tự phải không hợp lệ.
      */
      @Test
      @DisplayName("Biên dưới: 8 ký tự hợp lệ phải được chấp nhận")
      void exactMinimumLength_shouldReturnTrue() {
      String password = "Abcdef1@"; // Đúng 8 ký tự, đủ mọi thành phần
      assertEquals(8, password.length(), "Độ dài password test phải đúng 8 ký tự.");
      assertTrue(validator.isValid(password),
      "Mật khẩu đúng 8 ký tự hợp lệ phải được chấp nhận.");
      }

  @Test
  @DisplayName("Biên dưới -1: 7 ký tự phải bị từ chối")
  void oneBelowMinimumLength_shouldReturnFalse() {
  String password = "Abcde1@"; // Đúng 7 ký tự
  assertEquals(7, password.length(), "Độ dài password test phải đúng 7 ký tự.");
  assertFalse(validator.isValid(password),
  "Mật khẩu 7 ký tự phải bị từ chối.");
  }

  /**
    * Kiểm tra chính xác biên trên: 20 ký tự phải hợp lệ, 21 ký tự phải không hợp lệ.
      */
      @Test
      @DisplayName("Biên trên: 20 ký tự hợp lệ phải được chấp nhận")
      void exactMaximumLength_shouldReturnTrue() {
      String password = "A1@bcdefghijklmnopqt"; // Đúng 20 ký tự
      assertEquals(20, password.length(), "Độ dài password test phải đúng 20 ký tự.");
      assertTrue(validator.isValid(password),
      "Mật khẩu đúng 20 ký tự hợp lệ phải được chấp nhận.");
      }

  @Test
  @DisplayName("Biên trên +1: 21 ký tự phải bị từ chối")
  void oneAboveMaximumLength_shouldReturnFalse() {
  String password = "A1@bcdefghijklmnopqts"; // Đúng 21 ký tự
  assertEquals(21, password.length(), "Độ dài password test phải đúng 21 ký tự.");
  assertFalse(validator.isValid(password),
  "Mật khẩu 21 ký tự phải bị từ chối.");
  }
  }