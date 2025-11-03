*&---------------------------------------------------------------------*
*& 微信公众号配置表结构定义
*&---------------------------------------------------------------------*
*&
*& 说明：如果系统中有配置表 zticerp_wechat_config，可以使用
*& 如果没有，可以直接在构造函数中传入参数，无需此表
*&
*&---------------------------------------------------------------------*

"============================================================================
"方案一：创建配置表（如果需要在数据库中集中管理配置）
"============================================================================

"SE11 创建表：ZTICERP_WECHAT_CONFIG
"
"字段定义：
"   - MANDT     TYPE MANDT        "客户端
"   - CONFIG_KEY TYPE STRING      "配置键（主键）
"   - APPID     TYPE STRING       "微信公众号 AppID
"   - APPSECRET TYPE STRING       "微信公众号 AppSecret
"   - ZCRT_DATE TYPE DATS         "创建日期
"   - ZCRT_TIME TYPE TIMS         "创建时间
"   - ZUPD_DATE TYPE DATS         "更新日期
"   - ZUPD_TIME TYPE TIMS         "更新时间
"
"主键：MANDT, CONFIG_KEY
"
"示例数据：
"   CONFIG_KEY = 'OFFICIAL_ACCOUNT'
"   APPID      = 'wx1234567890abcdef'
"   APPSECRET  = 'your_appsecret_here'


"============================================================================
"方案二：不使用配置表，直接传入参数（推荐，更灵活）
"============================================================================

"不需要创建配置表，直接在创建实例时传入参数

"示例代码：
DATA(lo_wechat_api) = NEW zcl_wechat_official_api(
  iv_appid      = 'wx1234567890abcdef'      "直接传入 AppID
  iv_appsecret  = 'your_appsecret_here'    "直接传入 AppSecret
  iv_destination = 'WECHAT_OFFICIAL_API'    "可选
).

"然后直接使用
DATA(lv_token) = lo_wechat_api->get_wechat_token( ).


"============================================================================
"方案三：使用现有的配置表或参数表
"============================================================================

"如果系统中已有其他配置表，可以修改代码使用现有的表
"例如：ZTINT_PAR, ZTINT_CONFIG 等

"修改 CONSTRUCTOR 方法中的 SELECT 语句：
"
"   "从参数表读取
"   SELECT SINGLE value1 value2 INTO (mv_appid, mv_appsecret)
"     FROM ztint_par
"     WHERE fname = 'WECHAT_APPID'.
"
"   或者
"
"   "从自定义配置表读取
"   SELECT SINGLE config_value1 config_value2 INTO (mv_appid, mv_appsecret)
"     FROM ztint_config
"     WHERE config_type = 'WECHAT' AND config_key = 'OFFICIAL_ACCOUNT'.


"============================================================================
"推荐的实现方式（参考 ZCL_CORPWEIXIN_APPROVAL_API）
"============================================================================

"ZCL_CORPWEIXIN_APPROVAL_API 中的 CONSTRUCTOR 接收参数，不依赖特定配置表
"推荐做法：参数化，让调用者决定如何提供配置

"使用示例：
"方式1：直接传入参数（最常用）
DATA(lo_wechat1) = NEW zcl_wechat_official_api(
  iv_appid      = lv_appid
  iv_appsecret  = lv_appsecret
  iv_destination = 'WECHAT_API'
).

"方式2：从自定义配置读取后再传入
DATA: lv_custom_appid TYPE string,
      lv_custom_appsecret TYPE string.

"从任何配置源读取（表、文件、自定义逻辑等）
" ... 读取配置的代码 ...

DATA(lo_wechat2) = NEW zcl_wechat_official_api(
  iv_appid      = lv_custom_appid
  iv_appsecret  = lv_custom_appsecret
).

"方式3：不传参数，依赖构造函数中的可选读取逻辑
"（当前实现已用 TRY-CATCH 保护，即使表不存在也不会报错）
DATA(lo_wechat3) = NEW zcl_wechat_official_api( ).
"注意：如果配置表不存在且未传入参数，get_wechat_token 会返回空


"============================================================================
"建议
"============================================================================
"
"1. 如果系统有统一的配置管理机制，使用现有的配置表
"
"2. 如果没有配置表，建议直接在调用时传入参数（最灵活）
"
"3. 如果需要集中管理，可以创建简单的配置表，或使用系统参数表
"
"4. 代码已经用 TRY-CATCH 保护，即使表不存在也不会报错
"   但建议在调用 get_wechat_token 前检查配置是否已设置
"
"============================================================================

