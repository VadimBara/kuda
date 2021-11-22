@Interfeces

package Classes;
#1
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.TYPE;

@Retention(java.lang.annotation.RetentionPolicy.RUNTIME)
@Target({METHOD, TYPE})
@interface Selector {

        String xpath();
    }
#2
package Classes;

public interface MainPage {

    @Selector(xpath = ".//*[@test-attr='input_search']")
    String textInputSearch();

    @Selector(xpath = ".//*[@test-attr='button_search']")
    String buttonSearch();
   
}
@Classes

#1
package Classes;
import org.junit.Test;
import java.lang.reflect.Proxy;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotNull;


public class MethodInterception {

    @Test
    public void annotationValue() {
        MainPage mainPage = createPage();
        assertNotNull(mainPage);
        assertEquals(mainPage.buttonSearch(), ".//*[@test-attr='button_search']");
        assertEquals(mainPage.textInputSearch(), ".//*[@test-attr='input_search']");
    }

    private MainPage createPage() {
        //System.out.println("test");
        return (MainPage) Proxy.newProxyInstance(DoInvocationHandler.class.getClassLoader(),
                new Class [] {MainPage.class, Selector.class}, new DoInvocationHandler());
    }

}

#2
package Classes;

import java.lang.reflect.Method;
import java.lang.reflect.InvocationHandler;

public class DoInvocationHandler implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args)
            throws Throwable {
        return method.getAnnotation(Selector.class).xpath();
    }
}


