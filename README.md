package cucumber.features;

import org.openqa.selenium.Alert;
import org.openqa.selenium.By;
import org.openqa.selenium.NoSuchElementException;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.testng.Assert;
import cucumber.api.java.After;
import cucumber.api.java.en.Given;
import cucumber.api.java.en.Then;
import cucumber.api.java.en.When;

public class StepDefinitions {
	String url = "http://damp-sands-8561.herokuapp.com";
	String pageUrl = "";
	String successSignInMsg = "Signed in successfully.";
	String errorMessage = "Maximum entries reached for this date.";
	String successEntryMsg = "Entry was successfully created.";
	String userName = "raghu.yel002@gmail.com";
	String passWord = "codetheoryio";
	static String email = "user_email";
	static String password = "user_password";
	static String submit = "commit";
	static String successMsg = "//div[contains(text(),'Signed in successfully.')]";
	static String addEntryButton = "//a[contains(text(),'Add new')]";
	static String entryTextBox = "entry_level";
	int bloodGlucoseReadings[] = { 7, 8, 9, 6, 4 };
	static String deleteEntries = "//a[contains(.,'Delete')]";
	static String entrySuccessMsg = "//div[contains(text(),'Entry was successfully created')]";
	static String errorMsg = "//div[@class='alert alert-warning fade in']";
	static String infoMsg = "//div[@class='alert alert-info']";
	WebDriver driver = new FirefoxDriver();
	WebDriverWait wait = new WebDriverWait(driver, 10);

	@After
	public void afterScenario() {
		I_navigate_to("entryPage");
		try {
			while (driver.findElement(By.xpath(deleteEntries)).isDisplayed()) {
				driver.findElement(By.xpath(deleteEntries)).click();
				Alert alt = driver.switchTo().alert();
				alt.accept();
			}
		} catch (NoSuchElementException e) {
			System.out.println(e);
		}
		driver.manage().deleteAllCookies();
	}

	@Given("^Login to sweety application as a patient$")
	public void Login_to_sweety_application_as_a_patient() {
		driver.get(url);
		driver.manage().window().maximize();
		driver.findElement(By.id(email)).sendKeys(userName);
		driver.findElement(By.id(password)).sendKeys(passWord);
		driver.findElement(By.name(submit)).submit();
		// Verifying user is logged in successfully.
		Assert.assertTrue(driver.findElement(By.xpath(successMsg)).getText()
				.equalsIgnoreCase(successSignInMsg),
				"User is not registerd or entering wrong UserName or password");
	}

	@Given("^I navigate to \"([^\"]*)\"$")
	public void I_navigate_to(String arg) {
		String page = arg.toLowerCase();
		switch (page) {
		case "entrypage":
			pageUrl = "http://damp-sands-8561.herokuapp.com/entries";
			break;
		case "reportpage":
			pageUrl = "http://damp-sands-8561.herokuapp.com/report";
			break;

		default:
			pageUrl = page;
		}
		driver.get(pageUrl);
	}

	@When("^I enter blood glucose value for \"([^\"]*)\" time$")
	public void I_enter_blood_glucose_value_for_time(String noOfTimes) {
		int i = Integer.parseInt(noOfTimes);
		for (int j = 0; j < i; j++) {
			addEntries(bloodGlucoseReadings[j]);
		}
	}

	@Then("^I verify the success  message$")
	public void I_verify_the_success_message() {
		// Verifying success message after entering blood values.
		Assert.assertTrue(driver.findElement(By.xpath(entrySuccessMsg))
				.getText().equalsIgnoreCase(successEntryMsg),
				"Not able to enter valid blood entries");
	}

	@Then("^I verify the error message$")
	public void I_verify_the_error_message() {
		// Verify error message saying cannot enter more than 4 times a day
		Assert.assertTrue(driver.findElement(By.xpath(errorMsg)).getText()
				.contains(errorMessage),
				"Error message is not displaying after  4th entry");
	}

	public void addEntries(int bloodGlucoseReadings) {
		driver.findElement(By.xpath(addEntryButton)).click();
		wait.until(ExpectedConditions.visibilityOfElementLocated(By
				.id(entryTextBox)));
		driver.findElement(By.id(entryTextBox)).sendKeys(
				"" + bloodGlucoseReadings);
		driver.findElement(By.name(submit)).submit();
	}

}
