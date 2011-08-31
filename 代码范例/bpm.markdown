##范例1##
  try {
      IOrgMgmtService orgService = (IOrgMgmtService) ServiceManager.getInstance().getService(IOrgMgmtService.class);
    
    ......
  } 
  catch (Exception e) {
    e.printStackTrace();
    if (s_logger.isErrorEnabled()) {
      s_logger.error("CommonTree.generateTree.error", e);
    }
    throw new BPMBusinessException("CommonTree.generateTree.error", e);
  }

###建议1### 
取消 e.printStackTrace();  与 s_logger.error("CommonTree.generateTree.error", e) 功能重复

  try {
      IOrgMgmtService orgService = (IOrgMgmtService) ServiceManager.getInstance().getService(IOrgMgmtService.class);
    
    ......
  } 
  catch (Exception e) {
    if (s_logger.isErrorEnabled()) {
      s_logger.error("CommonTree.generateTree.error", e);
    }
    throw new BPMBusinessException("CommonTree.generateTree.error", e);
  }

###建议2###
取消 isErrorEnabled判断；isErrorEnabled判断是用来规避拼接错误信息耗时严重问题。 

try {
    IOrgMgmtService orgService = (IOrgMgmtService) ServiceManager.getInstance().getService(IOrgMgmtService.class);
	
	......
} 
catch (Exception e) {  
  s_logger.error("CommonTree.generateTree.error", e);
  throw new BPMBusinessException("CommonTree.generateTree.error", e);
}

###最终###
  try {
      IOrgMgmtService orgService = (IOrgMgmtService) ServiceManager.getInstance().getService(IOrgMgmtService.class);
    
    ......
  } 
  catch (Exception e) {
    String errormsg = "CommonTree.generateTree.error"
    s_logger.error(errormsg, e);
    throw new BPMBusinessException(errormsg, e);
  }

##范例2##
  List t_childrens = new ArrayList();
  for (int i = 0; i < domainList.size(); i++) {
    IDomain t_domain = (IDomain) domainList.get(i);
    ......
  }

###建议###

  List t_childrens = new ArrayList();
  for (Iterator it = t_childrens.iterator(); it.hasNext();) {
    IDomain t_domain = (IDomain) it.next();
    ......
  }

##范例3##
  List empList = null;

  IEmployeeManager t_employeeManager = (IEmployeeManager) ServiceManager.getInstance().getService(IEmployeeManager.class);
  QueryEmpBean t_bean = new QueryEmpBean();
  t_bean.setEmpname("%" + userName + "%");
  t_bean.setOrgid(orgId);

  empList = t_employeeManager.queryEmp(t_bean);

  if (empList!=null&&empList.size()>0){
    ......
  }

###建议1###
变量的声明尽可能在其赋值地方。

  IEmployeeManager t_employeeManager = (IEmployeeManager) ServiceManager.getInstance().getService(IEmployeeManager.class);
  QueryEmpBean t_bean = new QueryEmpBean();
  t_bean.setEmpname("%" + userName + "%");
  t_bean.setOrgid(orgId);

  List empList = t_employeeManager.queryEmp(t_bean);

  if (empList != null && empList.size() > 0){
    ......
  }

###建议2###
请使用isEmpty()判断list对象是否为空。

  IEmployeeManager t_employeeManager = (IEmployeeManager) ServiceManager.getInstance().getService(IEmployeeManager.class);
  QueryEmpBean t_bean = new QueryEmpBean();
  t_bean.setEmpname("%" + userName + "%");
  t_bean.setOrgid(orgId);

  List empList = t_employeeManager.queryEmp(t_bean);

  if (empList != null && !empList.isEmpty()){
    ......
  }


##范例4##
  if (eiBean.getStatus().equals("A")){
    ......
  }

###建议1###
规避eiBean可能为null时发生NullPointException

  if ("A".equals(eiBean.getStatus())){
    ......
  }

##范例5##
  try {
      IEmployeeManager t_employeeManager = (IEmployeeManager) ServiceManager.getInstance().getService(IEmployeeManager.class);    
      ......
  } catch (NotFoundModuleException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  } catch (ExpiryException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  } catch (LicenseException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  }catch (BPMBusinessException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  }

###建议1###
用log代替e.printStackTarce()

  try {
      IEmployeeManager t_employeeManager = (IEmployeeManager) ServiceManager.getInstance().getService(IEmployeeManager.class);
      ......
  } 
  catch (NotFoundModuleException e) {
    logger.warn("get IEmployeeManager service fail", e);
  } 
  catch (ExpiryException e) {
    logger.warn("get IEmployeeManager service fail", e);
  } 
  catch (LicenseException e) {
    logger.warn("get IEmployeeManager service fail", e);
  }
  catch (BPMBusinessException e) {
    logger.warn("get IEmployeeManager service fail", e);
  }

##范例6##

  import org.apache.commons.logging.Log;
  import org.apache.commons.logging.LogFactory;

  public class CommonTree {

    private static final Log logger = LogFactory.getLog(CommonTree.class);
    
    ......
OR

  import org.apache.log4j.Logger;

  public class CommonTree {
    
    private static Logger logger  = Logger.getLogger(CommonTree.class);

    ......


###建议###
使用slf4j代替log4j，commons-logging。

  import org.slf4j.Logger;
  import org.slf4j.LoggerFactory;

  public class CommonTree {

    private static final Logger logger = LoggerFactory.getLogger(CommonTree.class);

    ......


##范例6##
	OutputStream output = response.getOutputStream();
	try {
		......
		FileInputStream fin = new FileInputStream(f);
		try {
			......
			byte[] buf = new byte[1024];
			int r = 0;
			while ((r = fin.read(buf, 0, buf.length)) != -1) {
				output.write(buf, 0, r);
			}
			output.flush();
		}
		finally {
			fin.close();
		}
		return null;
	} catch (Exception e) {	
		logger.error("下载出错！");
		return null;
	} 
	
###建议1###
使用commons-io包中IOUtils

	OutputStream output = response.getOutputStream();
	try {
		......
		FileInputStream fin = new FileInputStream(f);
		try {
			......
			IOUtils.copyLarge(fin, output);
			output.flush();
		}
		finally {
			fin.close();
		}
		return null;
	} catch (Exception e) {	
		logger.error("下载出错！");
		return null;
	} 

###建议2###
在catch中记录日志，要把异常记录上。尽可能合并return

	OutputStream output = response.getOutputStream();
	try {
		......
		FileInputStream fin = new FileInputStream(f);
		try {
			......
			IOUtils.copyLarge(fin, output);
			output.flush();
		}
		finally {
			fin.close();
		}
	} 
	catch (Exception e) {	
		logger.error("下载出错！", e);
	} 
	return null;


##范例7##
	org.dom4j.io.XMLWriter xmlWriter = new org.dom4j.io.XMLWriter(out,fmt);
	doc.setXMLEncoding(sCharSet);
	xmlWriter.write(doc);
	xmlWriter.close();

###建议###
close函数请放入finally块，确保close不受write可能的异常而未执行。

	org.dom4j.io.XMLWriter xmlWriter = new org.dom4j.io.XMLWriter(out,fmt);
	try {
		doc.setXMLEncoding(sCharSet);
		xmlWriter.write(doc);
	}
	finally {
		xmlWriter.close();
	}

##范例8##

	private static Calendar calendar = Calendar.getInstance();

	public static Date backwordDay(Date d, int days) {
        calendar.setTime(d);
        calendar.add(Calendar.DATE, -days);
        return calendar.getTime();
    }

    public static Date forwordDay(Date d, int days) {
        calendar.setTime(d);
        calendar.add(Calendar.DATE, days);
        return calendar.getTime();
    }

###建议###
存在多线程安全问题。

	public static Date backwordDay(Date d, int days) {
		Calendar calendar = Calendar.getInstance();
        calendar.setTime(d);
        calendar.add(Calendar.DATE, -days);
        return calendar.getTime();
    }

    public static Date forwordDay(Date d, int days) {
		Calendar calendar = Calendar.getInstance();
        calendar.setTime(d);
        calendar.add(Calendar.DATE, days);
        return calendar.getTime();
    }

##范例9##

	public static String addDay(String strDate,String pattern,int days){
		if(strDate == null || "".equals(strDate) || pattern == null || "".equals(pattern) || strDate.length() < pattern.length()){
			return null;
		}
		String resultDate = null;

###建议###
判断字符串是否未空或null，请使用 org.apache.commons.lang.StringUtils

	public static String addDay(String strDate,String pattern,int days){
		if (StringUtils.isEmpty(strDate) || StringUtils.isEmpty(pattern) || strDate.length() < pattern.length()) {
			return null;
		}
		String resultDate = null;

##范例10##

	if(null != isEdit && !"".equals(isEdit) && "true".equals(isEdit)) {
		return mapping.findForward("draftPowerRateJsp");
	}

###建议###
使用org.apache.commons.lang.BooleanUtils

	if(BooleanUtils.toBoolean(isEdit)) {
		return mapping.findForward("draftPowerRateJsp");
	}
