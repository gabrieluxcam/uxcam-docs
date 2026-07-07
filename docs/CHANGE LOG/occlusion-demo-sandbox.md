---
title: Occlusion Demo Sandbox
excerpt: Hidden sandbox for testing the PhoneSimulator occlusion demo
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
---
# Occlusion Demo Sandbox

Hidden test page — verifying the PhoneSimulator side-by-side occlusion demo renders correctly before rolling it out to the platform occlusion guides.

## The demo

<div style={{ display: 'flex', flexWrap: 'wrap', justifyContent: 'center', gap: '32px', margin: '32px 0' }}>
  <div>
    <PhoneSimulator width="240px" height="480px" glow={false}>
      <div style={{ width: '100%', height: '100%', backgroundColor: '#fff', borderRadius: '20px', overflow: 'hidden', fontFamily: 'system-ui, sans-serif' }}>
        <div style={{ backgroundColor: '#4B2AFA', color: '#fff', padding: '18px 16px 10px', fontSize: '15px', fontWeight: 600 }}>Checkout</div>
        <div style={{ padding: '16px', display: 'flex', flexDirection: 'column', gap: '12px' }}>
          <div style={{ fontSize: '11px', color: '#666' }}>CARDHOLDER NAME</div>
          <div style={{ border: '1px solid #ddd', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#161E35' }}>Alex Rivera</div>
          <div style={{ fontSize: '11px', color: '#666' }}>CARD NUMBER</div>
          <div style={{ border: '1px solid #ddd', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#161E35' }}>4242 4242 4242 4242</div>
          <div style={{ fontSize: '11px', color: '#666' }}>CVV</div>
          <div style={{ border: '1px solid #ddd', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#161E35', width: '60px' }}>123</div>
          <div style={{ backgroundColor: '#4B2AFA', color: '#fff', borderRadius: '6px', padding: '10px', fontSize: '13px', fontWeight: 600, textAlign: 'center', marginTop: '8px' }}>Pay $49.00</div>
        </div>
      </div>
    </PhoneSimulator>
    <div style={{ textAlign: 'center', fontSize: '13px', marginTop: '8px', fontWeight: 700 }}>What your user sees</div>
  </div>
  <div>
    <PhoneSimulator width="240px" height="480px" glow={false}>
      <div style={{ width: '100%', height: '100%', backgroundColor: '#fff', borderRadius: '20px', overflow: 'hidden', fontFamily: 'system-ui, sans-serif' }}>
        <div style={{ backgroundColor: '#4B2AFA', color: '#fff', padding: '18px 16px 10px', fontSize: '15px', fontWeight: 600 }}>Checkout</div>
        <div style={{ padding: '16px', display: 'flex', flexDirection: 'column', gap: '12px' }}>
          <div style={{ fontSize: '11px', color: '#666' }}>CARDHOLDER NAME</div>
          <div style={{ backgroundColor: '#FA2A16', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#FA2A16' }}>occluded</div>
          <div style={{ fontSize: '11px', color: '#666' }}>CARD NUMBER</div>
          <div style={{ backgroundColor: '#FA2A16', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#FA2A16' }}>occluded</div>
          <div style={{ fontSize: '11px', color: '#666' }}>CVV</div>
          <div style={{ backgroundColor: '#FA2A16', borderRadius: '6px', padding: '8px 10px', fontSize: '13px', color: '#FA2A16', width: '60px' }}>occ</div>
          <div style={{ backgroundColor: '#4B2AFA', color: '#fff', borderRadius: '6px', padding: '10px', fontSize: '13px', fontWeight: 600, textAlign: 'center', marginTop: '8px' }}>Pay $49.00</div>
        </div>
      </div>
    </PhoneSimulator>
    <div style={{ textAlign: 'center', fontSize: '13px', marginTop: '8px', fontWeight: 700 }}>What UXCam records</div>
  </div>
</div>

## Content after the demo

If this heading and paragraph render as a normal page section (NOT inside the white phone area), the JSX block closed correctly and the demo is safe to roll out.
